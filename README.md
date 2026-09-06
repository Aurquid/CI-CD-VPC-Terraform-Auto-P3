# Overview
This project demonstrates a secure CI/CD pipeline using Terraform and GitHub actions with OIDC authentication. It automates VPC infrastructure and teardown while enforcing least-privilege IAM roles and managing remote state with S3 backend.

# Table of Contents
* [Overview](#overview)
* [Architecture Diagram](#architecture-diagram)
* [Pipeline Summary](#pipeline-summary)
* [Services Used](#services-used)
* [Folder Structure](#folder-structure)
* [Deployment Flow](#deployment-flow)
* [IAM Least Privilege](#iam-least-privilege)
* [Tradeoff Analysis](#tradeoff-analysis)
* [Cost Breakdown](#cost-breakdown)
* [Cost Analysis](#cost-analysis)
* [Architecture Tradeoffs](#architecture-tradeoffs)
* [Future Improvement suggestions ](#future-improvement-suggestions)
* [Failure Scenario & Recovery Playbook](#failure-scenario--recovery-playbook)
* [Lessons Learned](#lessons-learned)
* [Screenshots](#screenshots)

# Architecture Diagram

# Pipeline Summary
The CI/CD pipeline validates Terraform initialization ,plans resource changes, and applies to AWS while sharing a remote S3 backend with Project1. It authenticates through OIDC, IAM roles, and includes a manual destroy to prevent unintentional destruction. This setup provides validation and consistency to deployments but adds complexity to debugging  due to Terraform runs sent to GitHub Actions logs.

# Services Used
* S3( Terraform backend)
* IAM( OIDC role, EC2 SSM role)
* VPC
* EC2
* SSM
* GitHub Actions(CI/CD pipeline)
* CloudWatch

# Deployment Flow
## 1.Pipeline Trigger
* Workflow starts with workflow_dispatch
* GitHub Actions authenticates to AWS with OIDC
## 2. Terraform Initialization
* Terraform runs terraform init to connect to remote S3 backend and providers
## 3.Validation and Planning
* Executes terraform validate and terraform plan to  check syntax and show planned changes
## 4. Apply Stage
* Runs terraformapply -auto-approve to create resources
## 5. Post-Deployment Verification
* Terraform outputs confirm successful resources
## 6. Destroy Stage
* Triggered typing "DESTROY" when running workflow
* Runs terraform destroy-auto-approve using OIDC role and S3 backend
* Confirms planned destroy resources and ensure proper destruction 
  
 
# IAM Least Privilege
* GitHub Actions uses OIDC for short role-lived tokens
* Roles are separated under Terraform OIDC role and EC2 SSM role
* Each role limited to specific AWS services
* OIDC trust reserved to repo and branch
* No wildcard actions
* No admin privileges

# Cost Breakdown
| Component | Approx.Cost | Notes |
|---|---|---|
|EC2 t2.micro|$8-10/mo| charged while running|
|NAT Gateway| $32-$40/mo| biggest cost driver|
|S3 Backend| <$1/mo| Minimal cost|
|Elastic IP| $3-$4/mo| Static IP charge|
|IAM Roles & OIDC|$0/mo| No direct cost|
|SSM Access| $0| Free|
|GitHub Actions| $0-$5/mo| Depends on usage|

# Cost Analysis
## Usage-Based Cost Behavior
* **NAT Gateway** incurs the highest cost due to hourly costs and per-GB processed
* **EC2** costs remain stable unless instance type or region changes
* **S3 backend** costs are minimal due to state files being small and inconsistent access
* **IAM + OIDC** have no direct costs
* **SSM** access is free
## Cost Risks
* **High NAT traffic**  can increase costs the most
* **EC2** configuration can increase compute costs
* **Multiple environments** increase NAT+EC2 costs
* **Improper Destruction** can add idle costs
 ## Optimization Levers 
 * Replace NAT traffic with **VPC endpoints**
 * User **smaller EC2 instance types** for dev/test environments
 * Use **spot instances** for non-critical workloads
 * Add **scheduled destroy** to remove idle costs

# Architecture Tradeoffs
* **OIDC** vs **Long-Lived AWS Credentials:** OIDC removes the need for static AWS keys while improving security but can break easily if trust policy or sub claim is incorrect
* **S3 Backend** vs **Local Terraform State:** Remote state enables CI/CD automation but pipeline can crash if bucket name, region, or IAM is wrong
* **Least-Privilege IAM Roles** vs **Broad Permissions:** Least-privilege reduces attack surface under control with  permissions but can require more IAM debugging when something is denied
* **Full CI/CD** vs **Manual Terraform Deployment:** Automating resources adds validation and consistency to deployment but can be difficult to debug compared of local terminal
* **Separate IAM Roles** vs **One Shared Role:** Separate roles follows least-privilege but can add IAM complexity
* **CI/CD Destroy** vs **Manual Destroy:** CI/CD destroy provides clean destruction of resources but can be risky if not properly configured or triggered unintentially

# Future Improvement Suggestions
* Include cost-estimation stage with Infracost to estimate AWS costs before applying
* Add branch protection and environment approvals to deny unwanted applies or destroys
* Enable S3 backend versioning to preserve Terraform state history and simplify rolling back
  changes
* Include notifications via email or slack for pipeline success/failure
* Introduce Terraform modules for scalable code 
# Failure Scenario & Recovery Playbook
### Scenario 1
GitHub Actions fails to assume AWS role
### Recovery
* Verify trust policy JSON has the exact GitHub repo and branch name
* Confirm workflow permissions in YAML file
* Re-run workflow job after updates
### Scenario 2
Terraform cannot access or locate remote state 
### Recovery
* Check backend configuration
* Verify matching bucket name and IAM role permissions
* Run Terraform init to verify S3 backend is recognized
### Scenario 3
Terraform destroy fails to destroy proper resources
### Recovery
* Check the same S3 backend is shared with IAM permissions
* Verify right region is picked in Terraform-Destroy YAML file
* Valdiate and plan resources needed to be destroyed when running workflow
* Type "DESTROY" in proper caps to validate workflow run to destroy resources
# Lessons Learned
### OIDC Authentication
* Setting up OIDC requires underrstanding how GitHub Actions recognizes a workflow with token claims(iss,aud,sub)
* The trust policy must match the exact `sub` format to avoid AWS rejecting the `GithubOIDC-TerraformRole`
* OIDC removes the need for long-lived AWS keys
### S3 Remote State Backend
* Terraform must use the same `S3 backend bucket` for every run with GitHub Actions
* If backend changes then the `Terraform-Destroy.yml` will not destroy resources properly
### IAM Role Design for CI/CD
* Small JSON mistakes can break authentication with OIDC
* Following least-privlege keeps role secure and lower attack surface
### Pipeline Behavior and Terraform Execution
* `Terraform init` is the first place to identify backend issues
* `Terraform plan` in CI/CD is more strict with missing variables or backend configurations
* `Terraform apply` logs are essential to see what successfully deployed
### Destroying Infrastructure through CI/CD
* Destroy only works when the pipeline shares the same backend and role
* Missing state or wrong IAM roles prevents proper destruction
* Running `Terraform destroy` through CI/CD shows how Terraforms relies on remote sate to track every resource to destroy 
# Screenshots

### GitHub Actions
This screenshot shows the deployed infrastructure for all 26 resources via GitHub Actions with the outputs

<img width="1064" height="425" alt="Screenshot 2026-09-02 152457" src="https://github.com/user-attachments/assets/7c5774b3-b71f-4cc0-90b1-a80ff4b60861" />
Log confirms the OIDC token claims that is sent by GitHub Actions to AWS during authentication

<img width="1097" height="374" alt="Screenshot 2026-09-02 152425" src="https://github.com/user-attachments/assets/a2b6759b-cf34-411f-ba22-f2207481de6c" />
This screenshot shows terraform initialization successfully connected to the S3 backend 
<img width="796" height="411" alt="Screenshot 2026-09-02 152528" src="https://github.com/user-attachments/assets/f0947abc-b95d-4b16-af47-3e460f94dbab" />
T destroy
<img width="1106" height="523" alt="Screenshot 2026-09-02 160216" src="https://github.com/user-attachments/assets/87588850-3536-4d98-8f13-278329e57e74" />



### S3
The shared S3 bucket that is used for Project 3 from Project1
<img width="1112" height="482" alt="Screenshot 2026-09-02 153139" src="https://github.com/user-attachments/assets/338aa939-d637-401a-9034-9c855178382d" />
The S3 state.tfstate object inside the bucket that confirms the storage of the state file remotely 
<img width="1114" height="499" alt="Screenshot 2026-09-02 153117" src="https://github.com/user-attachments/assets/33ce227a-cd55-4a59-b523-9a011c667415" />


### IAM

The permissions granted to the `GithubOIDC-TerraformRole` based on the principle least-privilege 

<img width="1109" height="548" alt="Screenshot 2026-09-02 153013" src="https://github.com/user-attachments/assets/19350412-5c98-468c-b44f-e56482053cbe" />
The JSON trust policy for `GithubOIDC-TerraformRole`
<img width="1103" height="511" alt="Screenshot 2026-09-02 153030" src="https://github.com/user-attachments/assets/5d8b474c-03ca-474a-9e46-9bdde0744984" />
The SSM role used by the EC2 instances within the VPC to connect to  AWS Systems Manager
<img width="1116" height="564" alt="Screenshot 2026-09-02 154517" src="https://github.com/user-attachments/assets/d79cc468-c1ef-4f8d-a9e3-b008e43b2f90" />



### VPC
The VPC created by GitHub Actions shows successful deployment 
<img width="1115" height="569" alt="Screenshot 2026-09-02 153340" src="https://github.com/user-attachments/assets/957379c9-7e60-45b8-a96b-995a3bf572ad" />
The pubic and private subnets for the two instances
<img width="1116" height="543" alt="Screenshot 2026-09-02 153654" src="https://github.com/user-attachments/assets/9da794d8-862e-4537-9dbb-2b8c63bab6af" />
The route tables that is connected to the VPC
<img width="1114" height="566" alt="Screenshot 2026-09-02 153707" src="https://github.com/user-attachments/assets/24a8bb47-7240-4b17-b967-219d17d55b2d" />

The Internet Gateway(IGW) used by the VPC
<img width="1118" height="567" alt="Screenshot 2026-09-02 153720" src="https://github.com/user-attachments/assets/2c4b380d-82cd-4160-8bc8-b1c165039417" />
The NAT Gateway used by the VPC
<img width="1118" height="571" alt="Screenshot 2026-09-02 153745" src="https://github.com/user-attachments/assets/42e51a93-b271-465a-b0a8-25f8cb0ece51" />

The Security Groups for the Auto Load Balancer(ALB) and EC2
<img width="1118" height="568" alt="Screenshot 2026-09-02 153757" src="https://github.com/user-attachments/assets/a3749d95-cc89-4ebb-963e-ddb504a1fc2e" />
The Elastic IP address used by the NAT Gateway
<img width="1114" height="560" alt="Screenshot 2026-09-02 153832" src="https://github.com/user-attachments/assets/76719965-e04e-45ca-81c8-48cce54b52d6" />




### EC2
The two instances for the VPC running healthy
<img width="1119" height="564" alt="Screenshot 2026-09-02 154245" src="https://github.com/user-attachments/assets/b01e3602-8128-4388-9203-5fc85af1ef90" />

Load Balancer connected to the VPC that is used towards the EC2  
<img width="1115" height="563" alt="Screenshot 2026-09-02 154323" src="https://github.com/user-attachments/assets/a8ec2796-1d4b-4a4c-bdf7-94239947f7db" />

Target Groups that show that the EC2 instances passed the health checks
<img width="1114" height="565" alt="Screenshot 2026-09-02 154340" src="https://github.com/user-attachments/assets/89c459a6-c38f-4dbf-bb28-5fba8ba341f9" />

The Auto Scaling Group(ASG) details used by the EC2 instances
<img width="1103" height="557" alt="Screenshot 2026-09-02 154357" src="https://github.com/user-attachments/assets/288c4708-cc7c-4ec8-bfce-1300d40ebacd" />
