# ECS Reliability Blueprint – Terraform + GitHub Actions + CloudFront Functions

This repository demonstrates how to design and operate a modern, resilient, and production-ready architecture on AWS using **ECS**, **Terraform**, **CloudFront Functions**, **EventBridge**, **Lambda**, and **GitHub Actions**.

The case simulates a real-world scenario inspired by challenges I have faced as a Senior Site Reliability Engineer, showing a full flow from zero to production with emphasis on reliability, automation, and scalability.

---

##  Project Objective

Migrate a legacy application into a modern, scalable, automated, and observable environment using advanced SRE principles:

- Infrastructure-as-Code with **Terraform**
- Automated deployment with **GitHub Actions**
- AWS **ECS** (EC2 or Fargate) with autoscaling
- Edge routing and rewrite rules with **CloudFront Functions**
- Event-driven flows via **EventBridge + Lambda**
- Observability powered by CloudWatch logs, metrics, and alarms

This project serves as a complete blueprint for portfolio demonstration, onboarding, or architectural reference.

---

## High-Level Architecture

The final architecture includes:

- **VPC** with public and private subnets  
- **Application Load Balancer (ALB)**  
- **ECS Cluster + Service** running a containerized API  
- **Autoscaling** policies (CPU / ALB RequestCount)  
- **CloudFront** as CDN + edge layer  
- **CloudFront Function** for:
  - URL rewrites (legacy paths → modern API paths)
  - Security header injection
  - Lightweight request manipulation  
- **EventBridge** event bus for asynchronous events  
- **AWS Lambda** for event processing  
- **CloudWatch** for logs, dashboards, and monitoring  

---

## Repository Structure
```plaintext
ecs-reliability-blueprint/
├── app/
│ ├── src/
│ ├── Dockerfile
│ └── README.md
├── infra/
│ ├── main.tf
│ ├── variables.tf
│ ├── outputs.tf
│ ├── ecs/
│ ├── networking/
│ ├── cloudfront/
│ ├── eventbridge/
│ ├── lambda/
│ └── iam/
├── .github/
│ └── workflows/
│ ├── app-ci-cd.yml
│ └── infra-ci-cd.yml
├── diagrams/
│ └── architecture.png
└── README.md
```

---

## Application Overview

The sample API (Node.js or Python) implements:

- `GET /health`
- `GET /orders` – returns mock JSON
- `POST /orders` – logs the request and sends an EventBridge event

All logs follow **structured JSON** format for better monitoring and troubleshooting.

---

## 🛠️ Infrastructure Overview (Terraform)

### ECS
- ECS Cluster  
- Task Definition  
- ECS Service  
- Load balancer integration  
- Auto Scaling based on:
  - CPU Utilization  
  - ALB RequestCount per target  

### Networking
- VPC  
- Public and Private Subnets  
- Internet Gateway  
- Route Tables  
- Security Groups  

### CloudFront + Functions
- CloudFront Distribution pointing to ALB  
- CloudFront Function (JavaScript) for:
  - URL rewrites  
  - Security headers  

### EventBridge + Lambda
- Event Bus  
- Rules for event handling  
- Lambda function for order-created events  

### IAM
- ECS Task Role  
- Task Execution Role  
- Lambda Role  
- Strict least privilege policies  

---

## CI/CD with GitHub Actions

### Infrastructure Workflow (`infra-ci-cd.yml`)
- `terraform fmt`  
- `terraform validate`  
- `terraform plan`  
- Manual approval  
- `terraform apply`

### Application Workflow (`app-ci-cd.yml`)
- Docker build  
- Push image to ECR  
- ECS Service deployment  
- Rolling update with zero downtime  

Required secrets:

- `AWS_ACCESS_KEY_ID`  
- `AWS_SECRET_ACCESS_KEY`  
- `AWS_REGION`  
- `ECR_REPOSITORY`

---

## Observability & SRE Practices

### Recommended SLO
**99.5% uptime** for `/health`

### SLIs implemented
- Request success rate  
- ALB latency (P95)  
- ECS CPU/Memory usage  

### CloudWatch Alarms
- 5xx errors > 2% for 5 minutes  
- Latency P95 > 500ms  
- Lambda invocation failures > 0  

---

## How to Deploy

### 1. Clone the repository
```bash
git clone https://github.com/<your-user>/ecs-reliability-blueprint.git
