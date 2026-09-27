# MedPharma

## Cloud-Native Healthcare Platform | AWS · Terraform · EKS · Kubernetes · GitOps

[![AWS](https://img.shields.io/badge/AWS-Cloud-orange?logo=amazon-aws)](#)
[![Terraform](https://img.shields.io/badge/Terraform-Infrastructure%20as%20Code-7B42BC?logo=terraform)](#)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-EKS-326CE5?logo=kubernetes)](#)
[![Docker](https://img.shields.io/badge/Docker-Containers-2496ED?logo=docker)](#)
[![ArgoCD](https://img.shields.io/badge/ArgoCD-GitOps-EF7B4D?logo=argo)](#)
[![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-CI%2FCD-2088FF?logo=github)](#)

> **MedPharma is a cloud-native pharmaceutical platform engineered to demonstrate production-oriented AWS infrastructure, Kubernetes orchestration, Infrastructure as Code, CI/CD, GitOps, identity management, secrets management, and observability.**

**This repository contains the AWS infrastructure layer of the MedPharma platform.**

The project was designed and validated as a working AWS deployment before the demonstration environment was decommissioned to eliminate ongoing cloud infrastructure costs. The infrastructure is maintained as code so the environment can be reproduced when needed.

---

# 🚀 Project Highlights

MedPharma demonstrates the complete lifecycle of a modern cloud platform:

```text
Source Code
    │
    ▼
GitHub
    │
    ├──────────────────────┐
    │                      │
    ▼                      ▼
Application CI        Infrastructure CI
    │                      │
    ▼                      ▼
Docker / ECR           Terraform
    │                      │
    ▼                      ▼
GitOps                  AWS
    │                      │
    ▼                      ▼
ArgoCD                    EKS
    │                      │
    └──────────┬───────────┘
               ▼
          Application
               │
               ▼
              RDS
```

### Key Technologies

* **AWS** — VPC, EKS, RDS, ECR, IAM, S3, Secrets Manager
* **Terraform** — modular Infrastructure as Code
* **Kubernetes** — container orchestration through Amazon EKS
* **Docker** — application containerization
* **GitHub Actions** — CI/CD automation
* **ArgoCD** — GitOps-based Kubernetes deployment
* **Helm** — Kubernetes application packaging
* **PostgreSQL** — managed relational database
* **OIDC / IAM** — identity-based AWS access
* **NGINX Ingress** — Kubernetes ingress
* **Java 17 / Spring Boot** — backend microservices
* **React** — frontend application

---

# 📸 Deployment Evidence

The AWS demonstration environment was successfully provisioned and validated during development.

The environment was subsequently **decommissioned as a cost-control decision** rather than maintained as a continuously running portfolio environment.

Screenshots from the deployed environment are included below as evidence of the platform and its AWS components.

> **Current status:** The AWS environment is not continuously running. The infrastructure remains reproducible through Terraform.

### AWS Infrastructure

![AWS Infrastructure](docs/screenshots/aws-infrastructure.png)

### Amazon EKS

![Amazon EKS](docs/screenshots/eks-cluster.png)

### Amazon RDS

![Amazon RDS](docs/screenshots/rds.png)

### CI/CD

![GitHub Actions](docs/screenshots/github-actions.png)

### Application

![Application](docs/screenshots/application.png)

---

# 🏗️ Architecture

```text
                                  USERS
                                    │
                                    ▼
                           ┌─────────────────┐
                           │ AWS Load        │
                           │ Balancer        │
                           └────────┬────────┘
                                    │
                                    ▼
                           ┌─────────────────┐
                           │ NGINX Ingress   │
                           │ Controller      │
                           └────────┬────────┘
                                    │
                  ┌─────────────────┼──────────────────┐
                  │                 │                  │
                  ▼                 ▼                  ▼
             ┌─────────┐      ┌───────────┐     ┌─────────────┐
             │ React   │      │ API       │     │Notification │
             │Frontend │      │ Gateway   │     │ Service     │
             └─────────┘      └─────┬─────┘     └─────────────┘
                                    │
             ┌──────────────────────┼────────────────────────┐
             │                      │                        │
             ▼                      ▼                        ▼
       ┌──────────┐          ┌─────────────┐          ┌────────────┐
       │   Auth   │          │ Drug        │          │ Inventory  │
       │ Service  │          │ Catalog     │          │ Service    │
       └──────────┘          └─────────────┘          └────────────┘
             │                      │                        │
             └──────────────────────┼────────────────────────┘
                                    │
                                    ▼
                          ┌─────────────────────┐
                          │ Amazon RDS          │
                          │ PostgreSQL          │
                          │ Private Data Tier   │
                          └─────────────────────┘


                         AWS VPC
                       10.0.0.0/16
                            │
             ┌──────────────┼──────────────┐
             │              │              │
             ▼              ▼              ▼
       Public Tier     Private EKS     Private DB
       Subnets         Subnets         Subnets
             │              │              │
             │              ▼              ▼
             │           Amazon EKS      Amazon RDS
             │
             ▼
        Load Balancer
        / NAT Gateway
```

---

# ☁️ AWS Infrastructure

Terraform provisions the core AWS platform.

## Networking

* Amazon VPC
* Public subnets
* Private EKS subnets
* Private database subnets
* Internet Gateway
* NAT Gateway
* Route tables
* Security groups
* Network segmentation

### VPC CIDR

```text
10.0.0.0/16
│
├── Public
│   ├── 10.0.1.0/24
│   └── 10.0.2.0/24
│
├── Private EKS
│   ├── 10.0.3.0/24
│   └── 10.0.4.0/24
│
└── Private Database
    ├── 10.0.5.0/24
    └── 10.0.6.0/24
```

The architecture separates:

```text
Internet
   │
   ▼
Public Tier
   │
   ▼
Private EKS Tier
   │
   ▼
Private Database Tier
```

---

# ☸️ Amazon EKS

Amazon EKS provides the Kubernetes runtime for the platform.

The cluster is designed to host:

* API Gateway
* Authentication Service
* Drug Catalog Service
* Inventory Service
* Manufacturing Service
* Supplier Service
* Notification Service
* React frontend

Terraform provisions the underlying EKS infrastructure while GitOps manages application deployment configuration.

> **Terraform builds the platform. GitOps operates the workloads.**

---

# 🗄️ Data Layer

Amazon RDS PostgreSQL provides managed relational per
