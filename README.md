# Capability Lab 01 – Infrastructure Foundations

> Part of the Capability Labs series — demonstrating Infrastructure as Code (IaC) through modular, repeatable, controlled, and well-documented cloud infrastructure using Terraform.

---

## Overview

This repository is the first project in the **Capability Labs** series — a collection of focused engineering projects designed to develop and demonstrate practical skills in Platform Engineering, DevOps, Site Reliability Engineering (SRE), and Cloud Security.

**Infrastructure Foundations** focuses on Infrastructure as Code (IaC) using Terraform and Google Cloud Platform (GCP).

The project explores how cloud infrastructure can be provisioned consistently and reproducibly through version-controlled configuration rather than manual infrastructure changes.

The V1 architecture is designed for a growing organization where infrastructure changes need to be **reviewable, auditable, reproducible, and increasingly controlled as they approach Production**.

---

## Engineering Problem

As cloud environments grow, manually provisioned infrastructure becomes increasingly difficult to maintain consistently.

Manual infrastructure management can result in:

- Configuration drift between environments
- Inconsistent resource configuration
- Limited visibility into infrastructure changes
- Dependence on individual engineers
- Difficult environment reproduction
- Increased risk of accidental Production changes

Infrastructure as Code addresses these challenges by enabling infrastructure to be defined, versioned, reviewed, and deployed through code.

This project demonstrates that approach through Terraform and a controlled GitHub-based infrastructure workflow.

---

## Engineering Capability

At completion, this repository will demonstrate the ability to:

- Design cloud infrastructure using Infrastructure as Code
- Provision Google Cloud infrastructure using Terraform
- Build modular and reusable Terraform configurations
- Maintain separate Development, Pre-production, and Production environments
- Manage Terraform state remotely
- Use environment-specific Terraform execution identities
- Implement Pull Request-based infrastructure changes
- Automate deterministic Terraform validation and planning
- Apply progressively stronger controls toward Production
- Document engineering decisions, architecture, and trade-offs
- Verify infrastructure changes after deployment

---

## Architecture

The V1 architecture uses three separate GCP projects under the same organization:

```text
GCP Organization
│
├── Development
├── Pre-production
└── Production
```

Production is intentionally isolated from non-production infrastructure and Terraform state.

The infrastructure workflow follows:

```text
Change
  ↓
Pull Request
  ↓
Validation
  ↓
Terraform Plan
  ↓
Human Review
  ↓
Approval
  ↓
Environment Deployment
  ↓
Post-Deployment Verification
```

Deployment controls become progressively stronger as changes approach Production.

### Architecture Documentation

- [Engineering Design Review](docs/engineering-design-review.md)
- [Architecture](docs/architecture.md)

### Architecture Diagrams

- [Infrastructure Foundations Architecture](diagrams/infrastructure-foundations-architecture.svg)
- [Infrastructure Change & Deployment Workflow](diagrams/infrastructure-change-deployment-workflow.svg)

---

## Technology Stack

| Technology | Purpose |
|---|---|
| Terraform | Infrastructure as Code |
| Google Cloud Platform | Cloud infrastructure |
| Google Cloud Storage | Remote Terraform state |
| GitHub | Version control and Pull Requests |
| GitHub Actions | Infrastructure CI/CD automation |
| HCL | Terraform configuration |

---

## Repository Structure

```text
.
├── docs/
│   ├── EDR.md
│   └── architecture.md
│
├── diagrams/
│   ├── infrastructure-foundations-architecture.png
│   ├── infrastructure-foundations-architecture.svg
│   ├── infrastructure-change-deployment-workflow.png
│   └── infrastructure-change-deployment-workflow.svg
│
├── screenshots/
│
├── README.md
├── CHANGELOG.md
└── LICENSE
```

> The Terraform implementation structure will be added as implementation progresses.

---

## Project Status

🚧 **V1 Implementation — Starting**

### Completed

- Engineering Design Review
- V1 scope and objectives
- Environment architecture
- Terraform architecture
- Remote state strategy
- Environment-specific service-account strategy
- Infrastructure change and deployment workflow
- Production access and authorization model
- Architecture documentation
- Architecture diagrams

### Next

- Terraform repository structure
- GCP provider configuration
- Remote state implementation
- Environment configuration
- GCP infrastructure provisioning
- IAM and service-account configuration
- GitHub Actions workflows
- Environment-specific deployment controls
- Post-deployment verification

---

## Roadmap

### Phase 1 — Terraform Foundation
- Repository structure
- Terraform provider configuration
- Remote state
- Environment configuration
- Reusable modules

### Phase 2 — Infrastructure
- Compute
- Storage
- IAM
- Development environment
- Pre-production environment
- Production environment

### Phase 3 — Infrastructure Workflow
- Pull Request validation
- Terraform plan
- Human review
- Environment-specific Terraform apply
- Post-deployment verification

### Phase 4 — Production Controls
- Production project isolation
- Dedicated Production state boundary
- Production service account
- Restricted Production deployment
- Two-step Production authorization

### Phase 5 — Documentation & Demonstration
- Implementation evidence
- Failure and recovery demonstration
- Final architecture documentation
- End-to-end project demonstration

---

## Design Principles

The project is guided by several core principles:

> **Make the safe path the easy path.**

> **Minimize blast radius.**

> **Production isolation takes priority over marginal cost or administrative simplicity.**

> **Automation handles repeatable technical work; humans retain responsibility for authorization decisions.**

> **Infrastructure should be reproducible from version-controlled configuration.**

---

## Capability Labs

Infrastructure Foundations is **Capability Lab 01** in the Capability Labs series.

Future labs will build on the engineering practices developed here across areas such as:

- Platform Engineering
- Cloud Security
- DevOps
- Site Reliability Engineering
- Infrastructure Automation

---

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.
