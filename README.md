# Serverless-Web-Application-on-AWS

**Project type:** Serverless Web Application  
**Tech & AWS services:** Python, AWS Lambda, API Gateway, Amazon DynamoDB, S3 (static hosting), CloudFront, IAM, CloudWatch

## 🔎 Project Summary
Developed a serverless web application using AWS Lambda, API Gateway, DynamoDB, and S3. Backend logic is implemented in **Python** with an event-driven workflow to reduce manual effort. The app provides high scalability, secure API integration, zero server maintenance, and cost-efficient performance — resulting in a fast, reliable, fully managed cloud-native application.

 

## 🏗 Architecture  
1. **Clients** (Browser / Mobile) -> request to  
2. **Amazon CloudFront** -> routes to **S3** for static assets (frontend) or to  
3. **API Gateway** -> triggers **AWS Lambda (Python)** -> performs business logic and CRUD on **DynamoDB**.  
4. **CloudWatch** collects logs & metrics; **IAM** roles provide least-privilege access.

<img width="1105" height="436" alt="SS" src="https://github.com/user-attachments/assets/330493a7-1cfb-4bde-ab7b-b5c395b647f8" />
