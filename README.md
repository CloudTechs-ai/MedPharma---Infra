# MedPharma

## Cloud-Native Healthcare Platform | AWS · Terraform · EKS · Kubernetes · GitOps

[![AWS](https://img.shields.io/badge/AWS-Cloud-orange?logo=amazon-aws)](#)
[![Terraform](https://img.shields.io/badge/Terraform-Infrastructure%20as%20Code-7B42BC?logo=terraform)](#)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-EKS-326CE5?logo=kubernetes)](#)
[![Docker](https://img.shields.io/badge/Docker-Containers-2496ED?logo=docker)](#)
[![ArgoCD](https://img.shields.io/badge/ArgoCD-GitOps-EF7B4D?logo=argo)](#)
[![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-CI%2FCD-2088FF?logo=github)](#)

> **MedPharma is a production-style cloud engineering platform designed to demonstrate how a modern healthcare application can be provisioned, secured, containerized, deployed, and operated on AWS using Infrastructure as Code, Kubernetes, CI/CD, and GitOps.**

This repository is the **AWS infrastructure layer** of the MedPharma platform.

The goal is not simply to provision AWS resources.

The goal is to demonstrate the engineering practices required to build a **repeatable, secure, observable, and maintainable cloud platform**.

---

# ⚡ Executive Overview

### What was built?

A multi-tier AWS platform supporting a containerized healthcare/pharmaceutical application.

### How is it provisioned?

**Terraform** defines the AWS infrastructure as code.

### Where do workloads run?

**Amazon EKS** provides the Kubernetes runtime.

### Where does application data live?

**Amazon RDS PostgreSQL** provides managed persistent storage.

### How are containers delivered?

**GitHub Actions → Amazon ECR → GitOps → ArgoCD → EKS**

### How are secrets handled?

**AWS Secrets Manager** rather than credentials committed to source control.

### How is infrastructure changed?

**Pull Request → Terraform Plan → Review → Apply**

### What does this demonstrate?

Cloud architecture, networking, Kubernetes, Infrastructure as Code, CI/CD, GitOps, IAM, secrets management, containerization, and operational thinking.

---

# 🏗️ Architecture

```text
                                  USERS
                                    │
                                    ▼
                           ┌─────────────────┐
                           │  AWS Load       │
                           │  Balancer       │
                           └────────┬────────┘
                                    │
                                    ▼
                           ┌─────────────────┐
                           │ NGINX Ingress   │
                           │   Controller    │
                           └────────┬────────┘
                                    │
                  ┌─────────────────┼──────────────────┐
                  │                 │                  │
                  ▼                 ▼                  ▼
             ┌─────────┐      ┌───────────┐     ┌─────────────┐
             │ React   │      │    API    │     │Notification │
             │Frontend │      │  Gateway  │     │  Service    │
             └─────────┘      │   :8080   │     │    :8086    │
                              └─────┬─────┘     └─────────────┘
                                    │
             ┌──────────────────────┼────────────────────────┐
             │                      │                        │
             ▼                      ▼                        ▼
       ┌──────────┐          ┌─────────────┐          ┌────────────┐
       │   Auth   │          │    Drug     │          │ Inventory  │
       │  :8081   │          │   Catalog   │          │   :8083    │
       └────┬─────┘          │    :8082    │          └─────┬──────┘
            │                └──────┬──────┘                │
            │                       │                       │
            └───────────────────────┼───────────────────────┘
                                    │
                    ┌───────────────┴───────────────┐
                    │                               │
                    ▼                               ▼
             ┌─────────────┐                 ┌─────────────┐
             │Manufacturing│                 │  Supplier   │
             │    :8084    │                 │    :8085    │
             └──────┬──────┘                 └──────┬──────┘
                    │                               │
                    └───────────────┬───────────────┘
                                    │
                                    ▼
                          ┌─────────────────────┐
                          │    Amazon RDS       │
                          │    PostgreSQL       │
                          │    Private Tier     │
                          └─────────────────────┘


╔══════════════════════════════════════════════════════════════════════╗
║                         AWS PLATFORM                                ║
║                                                                      ║
║   PUBLIC SUBNETS              PRIVATE SUBNETS                       ║
║                                                                      ║
║   ┌───────────────┐            ┌──────────────────────────────┐     ║
║   │ Load Balancer │            │         Amazon EKS            │     ║
║   │ NAT Gateway   │            │      Worker Nodes             │     ║
║   └───────────────┘            └──────────────┬───────────────┘     ║
║                                               │                     ║
║                                               ▼                     ║
║                                  ┌──────────────────────────────┐   ║
║                                  │         Amazon RDS            │   ║
║                                  │          PostgreSQL           │   ║
║                                  └──────────────────────────────┘   ║
║                                                                      ║
╚══════════════════════════════════════════════════════════════════════╝
```

---

# 🔄 End-to-End Delivery Architecture

MedPharma separates **infrastructure delivery** from **application delivery** while connecting them through a common platform.

```text
                         SOURCE CONTROL
                              │
                ┌─────────────┴─────────────┐
                │                           │
                ▼                           ▼
       APPLICATION REPOS             INFRASTRUCTURE REPO
                │                           │
                ▼                           ▼
         GitHub Actions               GitHub Actions
                │                           │
                ▼                           ▼
         Security Checks                Terraform
                │                           │
                ▼                           ▼
            Docker                      AWS APIs
                │                           │
                ▼                           ▼
              ECR                    VPC / EKS / RDS
                │
                ▼
             GitOps
                │
                ▼
             ArgoCD
                │
                ▼
              EKS
                │
                ▼
         Running Workloads
```

This creates a clean separation of responsibilities:

| Layer              | Responsibility             |
| ------------------ | -------------------------- |
| **Terraform**      | AWS infrastructure         |
| **GitHub Actions** | Automation and validation  |
| **ECR**            | Container artifact storage |
| **GitOps**         | Desired Kubernetes state   |
| **ArgoCD**         | Kubernetes reconciliation  |
| **EKS**            | Application runtime        |
| **RDS**            | Persistent relational data |

---

# 🧠 Engineering Philosophy

The platform follows six core principles.

### 01 — Everything Important Is Code

Infrastructure should be reproducible rather than dependent on manual console configuration.

### 02 — Least Privilege

Applications, CI/CD pipelines, and infrastructure automation should receive only the AWS permissions they require.

### 03 — Private by Default

Application compute and databases are isolated inside private network tiers wherever possible.

### 04 — Immutable Artifacts

Container images are identified by immutable versions rather than relying on mutable deployment state.

### 05 — Git as the Source of Truth

Infrastructure and Kubernetes configuration are version controlled and reviewable.

### 06 — Automate the Feedback Loop

Changes should be validated before reaching shared infrastructure.

---

# ☁️ AWS Infrastructure

The Terraform configuration provisions the core AWS platform required by MedPharma.

## Networking

* Amazon VPC
* Public subnets
* Private application subnets
* Private database subnets
* Internet Gateway
* NAT Gateway
* Route tables
* Security groups

## Compute

* Amazon EKS
* EKS managed node groups
* Kubernetes networking
* IAM integration

## Data

* Amazon RDS PostgreSQL
* DB subnet groups
* Database security groups
* Private database connectivity

## Container Platform

* Amazon ECR
* Container image repositories
* Image lifecycle management

## Security

* AWS IAM
* IAM roles
* OIDC integration
* AWS Secrets Manager
* Kubernetes workload identity

---

# 🌐 Network Design

The development VPC uses:

```text
VPC
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
└── Private RDS Subnets
    ├── 10.0.5.0/24
    └── 10.0.6.0/24
```

Traffic is intentionally separated between:

```text
Internet-facing infrastructure
          │
          ▼
     Public Tier
          │
          ▼
    Private EKS Tier
          │
          ▼
    Private Data Tier
```

This provides clear network boundaries between ingress, compute, and persistent data.

---

# 🔐 Security Architecture

Security is treated as an architectural concern rather than a final-stage checklist.

## CI/CD Identity

Where supported, GitHub Actions can authenticate to AWS through OIDC:

```text
GitHub Actions
      │
      ▼
GitHub OIDC Token
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

This eliminates the need for long-lived static AWS credentials for supported workflows.

---

## Application Identity

Kubernetes workloads can obtain AWS permissions through workload identity:

```text
Pod
 │
 ▼
Kubernetes Service Account
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

This provides workload-level authorization without embedding AWS access keys into application containers.

---

## Secrets

Sensitive application configuration follows:

```text
AWS Secrets Manager
        │
        ▼
External Secrets
        │
        ▼
Kubernetes Secret
        │
        ▼
Application
```

Credentials should not be committed directly to Git.

---

# 🏗️ Terraform Design

The infrastructure is organized around reusable Terraform modules.

```text
med-infra/
│
├── envs/
│   ├── dev/
│   ├── qa/
│   └── prod/
│
├── modules/
│   ├── vpc/
│   ├── eks/
│   ├── rds/
│   ├── ecr/
│   ├── iam/
│   └── secrets-manager/
│
└── .github/
    └── workflows/
```

### Why Modules?

Instead of duplicating infrastructure configuration:

```text
              Shared Terraform Modules
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
         DEV           QA          PROD
```

Each environment consumes the same architectural building blocks while providing environment-specific configuration.

This makes infrastructure:

* easier to review
* easier to maintain
* easier to reproduce
* less prone to configuration drift
* easier to scale across environments

---

# 🔁 Infrastructure Change Management

Infrastructure changes follow a controlled lifecycle.

```text
Developer
    │
    ▼
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
Apply
    │
    ▼
AWS
```

The important idea is:

> **Infrastructure changes are reviewed before they become infrastructure.**

Terraform plans provide visibility into the expected impact of a change before applying it.

---

# 🗃️ Remote Terraform State

Terraform state is stored remotely in Amazon S3.

```text
Terraform
    │
    ▼
Amazon S3
    │
    ├── Encryption
    ├── Versioning
    └── Controlled Access
```

Remote state allows infrastructure automation to operate from a shared source of truth instead of relying on a developer's local machine.

---

# ☸️ Amazon EKS

EKS provides the Kubernetes execution environment for MedPharma.

```text
Amazon EKS
│
├── API Gateway
├── Auth Service
├── Drug Catalog
├── Inventory
├── Manufacturing
├── Supplier
├── Notification
└── Frontend
```

The infrastructure repository provisions the underlying AWS platform.

The GitOps repository manages application deployment configuration.

That separation keeps responsibilities clear:

> **Terraform builds the platform.
> GitOps operates the workloads.**

---

# 📦 Container Supply Chain

Application containers move through the platform as follows:

```text
Source Code
    │
    ▼
GitHub Actions
    │
    ├── Test
    ├── Security Scan
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

Images are versioned using immutable identifiers such as:

```text
auth-service:sha-a1b2c3d
```

rather than depending on:

```text
auth-service:latest
```

This makes deployed artifacts traceable to source-control revisions.

---

# 🔐 Defense-in-Depth

The platform applies security controls across multiple layers.

```text
                  INTERNET
                     │
                     ▼
              Load Balancer
                     │
                     ▼
                NGINX Ingress
                     │
                     ▼
             ┌───────────────┐
             │ Kubernetes    │
             │   Security    │
             └───────┬───────┘
                     │
                     ▼
               Private EKS
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

Identity and secrets are handled separately through:

```text
IAM
OIDC
Secrets Manager
Workload Identity
```

---

# 📊 Environment Strategy

The architecture is designed around isolated environments.

```text
                  Terraform Modules
                         │
          ┌──────────────┼──────────────┐
          │              │              │
          ▼              ▼              ▼
        DEV             QA            PROD
          │              │              │
          ▼              ▼              ▼
         AWS            AWS            AWS
```

Environment-specific configuration can control:

* compute capacity
* database sizing
* networking
* application configuration
* scaling parameters
* deployment settings

This allows the platform architecture to remain consistent while infrastructure characteristics change by environment.

---

# 💵 Cost Engineering

Cloud architecture has to account for cost as well as functionality.

The major development cost drivers include:

* EKS
* EC2 worker nodes
* NAT Gateway
* RDS
* Load Balancers
* ECR storage
* Secrets Manager

Temporary environments can be destroyed when no longer required:

```bash
terraform destroy
```

The objective is to avoid paying for infrastructure that is not actively providing value.

---

# 🛠️ Day-2 Operations

The infrastructure is designed to support ongoing operations, not just initial provisioning.

### Detect Drift

```bash
terraform plan
```

### Inspect State

```bash
terraform state list
```

### Inspect a Resource

```bash
terraform state show <resource>
```

### Validate Configuration

```bash
terraform fmt -check
terraform validate
```

### Review Infrastructure Changes

```bash
terraform plan
```

This provides a repeatable workflow for managing infrastructure throughout its lifecycle.

---

# 📁 Repository Structure

```text
med-infra/
│
├── envs/
│   ├── dev/
│   ├── qa/
│   └── prod/
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
├── README.md
├── versions.tf
├── providers.tf
└── variables.tf
```

---

# 🔗 MedPharma Platform

This repository is one component of the larger MedPharma engineering platform.

```text
                         MEDPHARMA
                            │
          ┌─────────────────┼─────────────────┐
          │                 │                 │
          ▼                 ▼                 ▼
      med-infra       backend services      frontend
          │                 │                 │
          ▼                 ▼                 │
       Terraform           Docker             │
          │                 │                 │
          ▼                 ▼                 │
        AWS/EKS             ECR ◄─────────────┘
          │                 │
          │                 ▼
          │               GitOps
          │                 │
          │                 ▼
          └─────────────► ArgoCD
                            │
                            ▼
                           EKS
```

### Repository Responsibilities

| Repository              | Responsibility                                        |
| ----------------------- | ----------------------------------------------------- |
| **med-infra**           | AWS infrastructure and Terraform                      |
| **med-pharma-backend**  | Java/Spring Boot microservices                        |
| **med-pharma-frontend** | React frontend                                        |
| **gitops**              | Kubernetes, Helm, and ArgoCD deployment configuration |

---

# 🧪 Technology Stack

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

**Java 17 · Spring Boot · PostgreSQL · React · Node.js**

---

# 🎯 What This Project Demonstrates to an Employer

A candidate who can build a project like this needs to understand considerably more than individual AWS services.

This platform demonstrates the ability to reason about:

* cloud architecture
* network segmentation
* Infrastructure as Code
* Kubernetes architecture
* containerized workloads
* IAM and identity
* secrets management
* CI/CD
* GitOps
* environment isolation
* infrastructure lifecycle management
* deployment traceability
* operational tradeoffs
* cloud cost considerations

More importantly, the architecture demonstrates how these technologies fit together into **one operating system for a cloud platform**, rather than existing as disconnected technologies on a resume.

---

# 🏆 Engineering Principles

| Principle                     | Implementation                 |
| ----------------------------- | ------------------------------ |
| **Infrastructure as Code**    | Terraform                      |
| **Immutable Infrastructure**  | Terraform-managed resources    |
| **Declarative Deployment**    | Kubernetes + GitOps            |
| **Continuous Reconciliation** | ArgoCD                         |
| **Containerization**          | Docker                         |
| **Artifact Management**       | Amazon ECR                     |
| **Identity-Based Security**   | IAM / OIDC                     |
| **Secret Management**         | AWS Secrets Manager            |
| **Network Isolation**         | Public / Private Subnets       |
| **Managed Persistence**       | Amazon RDS                     |
| **Automated Validation**      | GitHub Actions                 |
| **Change Review**             | Pull Requests + Terraform Plan |
| **Environment Isolation**     | Dev / QA / Prod                |

---

# 🚀 Platform Lifecycle

The complete lifecycle looks like this:

```text
                     ┌───────────────┐
                     │   Developer   │
                     └───────┬───────┘
                             │
                             ▼
                          GitHub
                             │
             ┌───────────────┴───────────────┐
             │                               │
             ▼                               ▼
       Application CI                  Infrastructure CI
             │                               │
             ▼                               ▼
           Docker                         Terraform
             │                               │
             ▼                               ▼
            ECR                        AWS Infrastructure
             │                               │
             ▼                               │
           GitOps                            │
             │                               │
             ▼                               │
           ArgoCD                            │
             │                               │
             └───────────────┬───────────────┘
                             ▼
                           EKS
                             │
               ┌─────────────┼─────────────┐
               │             │             │
               ▼             ▼             ▼
            Services      Frontend       Ingress
               │
               ▼
              RDS
```

---

# 💡 The Big Picture

MedPharma is intentionally designed as more than a collection of cloud resources.

It demonstrates a complete engineering workflow:

```text
DESIGN
  ↓
TERRAFORM
  ↓
AWS
  ↓
EKS
  ↓
CONTAINERS
  ↓
ECR
  ↓
GITOPS
  ↓
ARGOCD
  ↓
APPLICATION
  ↓
OBSERVE / OPERATE
  ↓
ITERATE
```

The result is a **reproducible cloud platform architecture** where infrastructure, applications, deployment configuration, and operational workflows are managed as code.

---

## Core Technologies

**AWS · Terraform · Kubernetes · Amazon EKS · Docker · Amazon ECR · Amazon RDS · PostgreSQL · IAM · OIDC · Secrets Manager · GitHub Actions · Helm · ArgoCD · NGINX**

---

### Built by CloudTechs.ai

**Cloud Engineering · Infrastructure as Code · Kubernetes · DevOps · Cloud Security**
