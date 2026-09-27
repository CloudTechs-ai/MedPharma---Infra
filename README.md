# MedPharma

## Cloud-Native Healthcare Platform | AWS · Terraform · EKS · Kubernetes · GitOps

[![AWS](https://img.shields.io/badge/AWS-Cloud-orange?logo=amazon-aws)](#)
[![Terraform](https://img.shields.io/badge/Terraform-Infrastructure%20as%20Code-7B42BC?logo=terraform)](#)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-EKS-326CE5?logo=kubernetes)](#)
[![Docker](https://img.shields.io/badge/Docker-Containers-2496ED?logo=docker)](#)
[![ArgoCD](https://img.shields.io/badge/ArgoCD-GitOps-EF7B4D?logo=argo)](#)
[![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-CI%2FCD-2088FF?logo=github)](#)

> **MedPharma is a cloud-native pharmaceutical platform built to demonstrate modern AWS infrastructure, Infrastructure as Code, Kubernetes, containerization, CI/CD, GitOps, cloud security, and operational engineering.**

This repository contains the **AWS infrastructure layer** of the MedPharma platform.

The project was designed around a production-oriented architecture in which infrastructure, application delivery, Kubernetes configuration, and deployment workflows are managed as code.

---

# 🚀 Project Overview

MedPharma combines multiple engineering disciplines into a single cloud platform:

```text
Source Code
     │
     ▼
   GitHub
     │
     ├──────────────────────┐
     │                      │
     ▼                      ▼
Application Repositories   Infrastructure
     │                      │
     ▼                      ▼
GitHub Actions            Terraform
     │                      │
     ▼                      ▼
Docker / ECR              AWS
     │                      │
     ▼                      ▼
   GitOps                   EKS
     │                      │
     ▼                      │
   ArgoCD                   │
     │                      │
     └──────────┬───────────┘
                ▼
           Application
                │
                ▼
               RDS
```

### Core Platform Components

* **AWS VPC** — network architecture and segmentation
* **Amazon EKS** — Kubernetes runtime
* **Amazon RDS PostgreSQL** — persistent application data
* **Amazon ECR** — container image storage
* **AWS IAM / OIDC** — identity and access management
* **AWS Secrets Manager** — sensitive configuration
* **Terraform** — Infrastructure as Code
* **Docker** — containerization
* **GitHub Actions** — CI/CD automation
* **ArgoCD** — GitOps deployment
* **Kubernetes / Helm** — workload management
* **NGINX Ingress** — application ingress
* **CloudWatch / Grafana** — observability

---

# 📸 Application Screenshots

The following screenshots were captured from the completed MedPharma application during its development and validation phase.

The AWS environment used for validation was subsequently **decommissioned as a cost-control decision** rather than maintained as a continuously running portfolio environment.

The screenshots demonstrate the completed application experience, while the infrastructure and deployment repositories preserve the engineering implementation.

### Platform Dashboard

![MedPharma Dashboard](screenshots/dashboard.png)

### Distribution

![Distribution](screenshots/distribution.png)

### Inventory

![Inventory](screenshots/inventory.png)

### Drug Catalog

![Drug Catalog](screenshots/drug-catalog.png)

> **Deployment status:** The application was previously deployed and validated in AWS. The live AWS environment is not currently running because the demonstration infrastructure was decommissioned to eliminate ongoing cloud costs. The infrastructure remains defined through Terraform and can be recreated when required.

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
```

---

# ☁️ AWS Infrastructure

Terraform provisions the core AWS platform required by MedPharma.

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
├── Public Subnets
│   ├── 10.0.1.0/24
│   └── 10.0.2.0/24
│
├── Private EKS Subnets
│   ├── 10.0.3.0/24
│   └── 10.0.4.0/24
│
└── Private Database Subnets
    ├── 10.0.5.0/24
    └── 10.0.6.0/24
```

The network is separated into three primary tiers:

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

This provides clear separation between externally accessible infrastructure, application workloads, and persistent data.

---

# ☸️ Amazon EKS

Amazon EKS provides the Kubernetes runtime for the platform.

The cluster is designed to support:

* API Gateway
* Authentication Service
* Drug Catalog Service
* Inventory Service
* Supplier Service
* Notification Service
* React frontend

Terraform manages the underlying AWS infrastructure while the GitOps repository manages Kubernetes deployment configuration.

> **Terraform builds the platform. GitOps operates the workloads.**

---

# 🗄️ Data Layer

Amazon RDS PostgreSQL provides managed relational persistence.

The database is designed to operate inside the private database tier and communicate with application workloads through controlled network paths and security groups.

```text
EKS Workloads
      │
      ▼
Security Groups
      │
      ▼
Private RDS
      │
      ▼
PostgreSQL
```

---

# 🔐 Security Architecture

Security is incorporated throughout the platform architecture.

## GitHub Actions → AWS

```text
GitHub Actions
      │
      ▼
GitHub OIDC
      │
      ▼
AWS IAM Role
      │
      ▼
Temporary Credentials
      │
      ▼
AWS APIs
```

OIDC-based authentication allows supported CI/CD workflows to authenticate to AWS without storing long-lived AWS access keys in GitHub.

## Kubernetes Workload Identity

```text
Kubernetes Pod
      │
      ▼
Service Account
      │
      ▼
OIDC
      │
      ▼
AWS IAM Role
      │
      ▼
AWS Service
```

## Secrets Management

Sensitive configuration is designed to be stored in AWS Secrets Manager rather than committed directly to source control.

```text
AWS Secrets Manager
        │
        ▼
Kubernetes Secret
        │
        ▼
Application
```

---

# 🏗️ Terraform Architecture

The infrastructure is organized into reusable Terraform modules.

```text
infra/
│
├── envs/
│   └── dev/
│
├── modules/
│   ├── vpc/
│   ├── eks/
│   ├── rds/
│   ├── ecr/
│   ├── iam/
│   └── secrets-manager/
│
├── .github/
│   └── workflows/
│
├── providers.tf
├── versions.tf
└── README.md
```

### Module Responsibilities

| Module            | Responsibility                              |
| ----------------- | ------------------------------------------- |
| `vpc`             | VPC, subnets, routing, NAT, and networking  |
| `eks`             | EKS cluster and managed node infrastructure |
| `rds`             | PostgreSQL database infrastructure          |
| `ecr`             | Container image repositories                |
| `iam`             | AWS identities and workload permissions     |
| `secrets-manager` | Application secret storage                  |

The modular architecture makes the infrastructure easier to reproduce, review, maintain, and extend.

---

# 🔄 CI/CD & GitOps

Application delivery and infrastructure delivery are intentionally separated.

```text
                         GitHub
                            │
             ┌──────────────┴──────────────┐
             │                             │
             ▼                             ▼
     Application Repos               Infrastructure
             │                             │
             ▼                             ▼
      GitHub Actions                 GitHub Actions
             │                             │
             ▼                             ▼
          Docker                     Terraform Plan
             │                             │
             ▼                             ▼
            ECR                           AWS
             │
             ▼
           GitOps
             │
             ▼
           ArgoCD
             │
             ▼
            EKS
```

## Infrastructure Workflow

```text
Feature Branch
      │
      ▼
Pull Request
      │
      ├── Terraform Format
      ├── Terraform Validate
      ├── Security Checks
      └── Terraform Plan
      │
      ▼
Code Review
      │
      ▼
Merge
      │
      ▼
Terraform Apply
      │
      ▼
AWS
```

This provides an auditable workflow where infrastructure changes can be reviewed before they are applied.

---

# 📦 Container Supply Chain

Application containers follow a traceable delivery path:

```text
Source Code
    │
    ▼
GitHub Actions
    │
    ├── Test
    ├── Validate
    └── Build
    │
    ▼
Docker Image
    │
    ▼
Amazon ECR
    │
    ▼
GitOps
    │
    ▼
ArgoCD
    │
    ▼
Amazon EKS
```

Container versions can be tied to source-control revisions rather than relying exclusively on mutable tags such as `latest`.

---

# 📊 Observability

The platform incorporates operational visibility through AWS and Kubernetes tooling.

Examples include:

* Amazon CloudWatch
* Kubernetes workload status
* Application health checks
* Container logs
* Grafana dashboards
* Infrastructure metrics

The goal is to make deployed systems observable throughout their lifecycle.

---

# 💰 Cost-Aware Cloud Engineering

Cloud infrastructure has an ongoing operating cost, particularly when combining:

* EKS
* EC2 worker nodes
* NAT Gateway
* RDS
* Load Balancers
* ECR
* Observability services

For a portfolio project, maintaining production-style infrastructure continuously after validation provides limited additional value while continuing to generate recurring costs.

The demonstration environment was therefore **decommissioned after validation**.

This keeps the infrastructure reproducible without maintaining unnecessary cloud spend.

```text
Terraform
    │
    ▼
Infrastructure Definition
    │
    ▼
Reproducible AWS Environment
```

This is an intentional part of the project's lifecycle rather than a limitation of the architecture.

---

# 🧪 Infrastructure Validation

Terraform provides the primary infrastructure validation workflow.

```bash
terraform fmt -check
terraform validate
terraform plan
```

Infrastructure state and individual resources can also be inspected using:

```bash
terraform state list
terraform state show <resource>
```

The platform is designed around declarative infrastructure rather than manual AWS console configuration.

---

# 📁 Repository Ecosystem

MedPharma is divided into multiple repositories so infrastructure, application code, and deployment configuration remain independently maintainable.

| Repository              | Responsibility                             |
| ----------------------- | ------------------------------------------ |
| **med-infra**           | AWS infrastructure and Terraform           |
| **med-pharma-backend**  | Java / Spring Boot microservices           |
| **med-pharma-frontend** | React frontend                             |
| **gitops**              | Kubernetes, Helm, and ArgoCD configuration |

This separation mirrors a modern cloud engineering workflow in which infrastructure and application responsibilities are managed independently.

---

# 🎯 Engineering Skills Demonstrated

## Cloud Architecture

* AWS VPC architecture
* Public/private subnet design
* Amazon EKS
* Amazon RDS
* Amazon ECR
* Load balancing
* Cloud networking

## Infrastructure as Code

* Terraform
* Reusable modules
* Remote state
* Environment configuration
* Infrastructure lifecycle management

## Kubernetes

* Amazon EKS
* Kubernetes workloads
* Ingress
* Service accounts
* Helm
* GitOps

## DevOps

* GitHub Actions
* CI/CD
* Docker
* Amazon ECR
* Automated Terraform validation
* Deployment workflows

## Cloud Security

* IAM
* OIDC
* Workload identity
* Secrets Manager
* Network segmentation
* Least-privilege architecture

## Operations

* CloudWatch
* Grafana
* Logging
* Health checks
* Infrastructure drift detection
* Cost-aware infrastructure lifecycle management

---

# 🔗 MedPharma Platform

The infrastructure repository is one component of the larger MedPharma platform.

```text
                         MEDPHARMA
                             │
          ┌──────────────────┼──────────────────┐
          │                  │                  │
          ▼                  ▼                  ▼
      med-infra           backend           frontend
          │                  │                  │
          ▼                  ▼                  │
       Terraform           Docker              │
          │                  │                  │
          ▼                  ▼                  │
        AWS/EKS             ECR ◄──────────────┘
          │                  │
          │                  ▼
          │                GitOps
          │                  │
          │                  ▼
          └───────────────► ArgoCD
                             │
                             ▼
                            EKS
                             │
                             ▼
                            RDS
```

---

# 🛠️ Technology Stack

### Cloud

**AWS · VPC · EKS · RDS · ECR · IAM · S3 · Secrets Manager**

### Infrastructure

**Terraform · Terraform Modules · Remote State**

### Containers

**Docker · Amazon ECR**

### Kubernetes

**Kubernetes · EKS · Helm · NGINX Ingress**

### CI/CD

**GitHub Actions**

### GitOps

**ArgoCD · Git-based Desired State**

### Application

**Java 17 · Spring Boot · PostgreSQL · React**

---

# 💡 Project Objective

MedPharma was built to demonstrate how a cloud engineer can combine individual technologies into a cohesive cloud platform.

The project brings together:

```text
Cloud Architecture
        ↓
Infrastructure as Code
        ↓
Network Segmentation
        ↓
Identity & Security
        ↓
Containers
        ↓
Kubernetes
        ↓
CI/CD
        ↓
GitOps
        ↓
Observability
        ↓
Cost Management
```

The result is a **reproducible cloud-native platform architecture** where infrastructure, application delivery, deployment configuration, and operational workflows are managed as code.

---

# 📌 Project Status

**Development and validation completed.**

The MedPharma application and supporting AWS infrastructure were developed and validated during the project lifecycle.

The AWS demonstration environment was subsequently decommissioned to eliminate recurring infrastructure costs.

The current repository contains the infrastructure implementation and documentation required to understand and reproduce the platform.

Application screenshots are included in:

```text
screenshots/
```

---

## Core Technologies

**AWS · Terraform · Kubernetes · Amazon EKS · Docker · Amazon ECR · Amazon RDS · PostgreSQL · IAM · OIDC · Secrets Manager · GitHub Actions · Helm · ArgoCD · NGINX · Java 17 · Spring Boot · React**

---

### Built by CloudTechs.ai

**Cloud Engineering · Infrastructure as Code · Kubernetes · DevOps · Cloud Security**
