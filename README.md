# Overview

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
* [Cost & Security Considerations](#cost--security-considerations)
* [Architecture Tradeoffs](#architecture-tradeoffs)
* [Future Improvements](#future-improvements)
* [Failure Scenario & Recovery Playbook](#failure-scenario--recovery-playbook)
* [Lessons Learned](#lessons-learned)
* [Screenshots](#screenshots)

# Architecture Diagram

# Pipeline Summary

# Services Used

# Folder Structure

# Deployment Flow

# IAM Least Privilege

# Tradeoff Analysis

# Cost Breakdown

# Cost Analysis

# Cost & Security Considerations

# Architecture Tradeoffs

# Future Improvements

# Failure Scenario & Recovery Playbook

# Lessons Learned

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
<img width="1112" height="482" alt="Screenshot 2026-09-02 153139" src="https://github.com/user-attachments/assets/338aa939-d637-401a-9034-9c855178382d" />
<img width="1114" height="499" alt="Screenshot 2026-09-02 153117" src="https://github.com/user-attachments/assets/33ce227a-cd55-4a59-b523-9a011c667415" />


### IAM



<img width="1109" height="548" alt="Screenshot 2026-09-02 153013" src="https://github.com/user-attachments/assets/19350412-5c98-468c-b44f-e56482053cbe" />

<img width="1103" height="511" alt="Screenshot 2026-09-02 153030" src="https://github.com/user-attachments/assets/5d8b474c-03ca-474a-9e46-9bdde0744984" />
<img width="1116" height="564" alt="Screenshot 2026-09-02 154517" src="https://github.com/user-attachments/assets/d79cc468-c1ef-4f8d-a9e3-b008e43b2f90" />



### VPC
<img width="1115" height="569" alt="Screenshot 2026-09-02 153340" src="https://github.com/user-attachments/assets/957379c9-7e60-45b8-a96b-995a3bf572ad" />

<img width="1116" height="543" alt="Screenshot 2026-09-02 153654" src="https://github.com/user-attachments/assets/9da794d8-862e-4537-9dbb-2b8c63bab6af" />

<img width="1114" height="566" alt="Screenshot 2026-09-02 153707" src="https://github.com/user-attachments/assets/24a8bb47-7240-4b17-b967-219d17d55b2d" />


<img width="1118" height="567" alt="Screenshot 2026-09-02 153720" src="https://github.com/user-attachments/assets/2c4b380d-82cd-4160-8bc8-b1c165039417" />

<img width="1118" height="571" alt="Screenshot 2026-09-02 153745" src="https://github.com/user-attachments/assets/42e51a93-b271-465a-b0a8-25f8cb0ece51" />


<img width="1118" height="568" alt="Screenshot 2026-09-02 153757" src="https://github.com/user-attachments/assets/a3749d95-cc89-4ebb-963e-ddb504a1fc2e" />

<img width="1114" height="560" alt="Screenshot 2026-09-02 153832" src="https://github.com/user-attachments/assets/76719965-e04e-45ca-81c8-48cce54b52d6" />




### EC2
<img width="1119" height="564" alt="Screenshot 2026-09-02 154245" src="https://github.com/user-attachments/assets/b01e3602-8128-4388-9203-5fc85af1ef90" />

<img width="1108" height="563" alt="Screenshot 2026-09-02 154305" src="https://github.com/user-attachments/assets/c0a95342-94d7-445c-926f-b9b78cdea3a0" />


<img width="1115" height="563" alt="Screenshot 2026-09-02 154323" src="https://github.com/user-attachments/assets/a8ec2796-1d4b-4a4c-bdf7-94239947f7db" />


<img width="1114" height="565" alt="Screenshot 2026-09-02 154340" src="https://github.com/user-attachments/assets/89c459a6-c38f-4dbf-bb28-5fba8ba341f9" />


<img width="1103" height="557" alt="Screenshot 2026-09-02 154357" src="https://github.com/user-attachments/assets/288c4708-cc7c-4ec8-bfce-1300d40ebacd" />
