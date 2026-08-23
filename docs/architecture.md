# Infrastructure Foundations — Architecture

## 1. Architecture Overview

Infrastructure Foundations uses Terraform to provision and manage foundational Google Cloud infrastructure through a version-controlled engineering workflow.

The architecture consists of:

- GitHub for version control and Pull Requests
- GitHub Actions for automated Terraform validation and execution
- Terraform for Infrastructure as Code
- Google Cloud projects for environment isolation
- Google Cloud Storage for remote Terraform state
- Environment-specific service accounts for Terraform execution

The overall flow is:

Engineer → GitHub → GitHub Actions → Terraform → Google Cloud

Terraform state is maintained separately from the infrastructure resources it manages.

### Architecture Diagram

![Infrastructure Foundations Architecture](diagrams/infrastructure-foundations-architecture.png)

[SVG version](diagrams/infrastructure-foundations-architecture.svg)

---

## 2. Environment Architecture

Development, Pre-production, and Production are maintained as separate Google Cloud projects under the same organization.

```text
GCP Organization
│
├── Development Project
│
├── Pre-production Project
│
└── Production Project
```

### Development

Development is the least restrictive environment and is intended for rapid iteration, experimentation, and infrastructure validation.

Engineers can modify Development infrastructure through the standard Pull Request workflow.

### Pre-production

Pre-production provides an additional validation boundary before Production.

Infrastructure changes follow the standard workflow but require additional review before deployment.

### Production

Production is treated as a separate trust and blast-radius boundary.

Production has:

- A dedicated GCP project
- A dedicated Terraform state storage boundary
- A dedicated Terraform service account
- Restricted deployment access
- Two-step authorization before deployment

### Design Principle

> **The closer a change gets to Production, the stronger the control boundary becomes.**

---

## 3. Terraform & State Architecture

Terraform uses reusable modules together with environment-specific root configurations.

```text
terraform/
│
├── modules/
│   ├── compute/
│   ├── storage/
│   └── iam/
│
└── environments/
    ├── dev/
    ├── preprod/
    └── prod/
```

### Terraform Modules

Reusable modules define common infrastructure patterns that can be consumed by multiple environments.

### Environment Configurations

Each environment has its own root Terraform configuration containing its environment-specific configuration, variables, provider configuration, and state backend.

This allows infrastructure patterns to be reused while still allowing deliberate differences between environments.

### Remote State

Terraform state is stored remotely in Google Cloud Storage rather than on individual engineer machines.

Development and Pre-production may share a non-production storage boundary provided their Terraform state remains logically separated.

Production uses a dedicated state storage boundary.

```text
Terraform
   │
   ├── Development State
   │
   ├── Pre-production State
   │
   └── Production State
          │
          └── Dedicated storage boundary
```

### Terraform Execution Identities

Terraform uses environment-specific service accounts:

```text
Terraform Dev Service Account
Terraform Pre-prod Service Account
Terraform Prod Service Account
```

Each service account is scoped to its corresponding environment and granted only the permissions required for Terraform operations in that environment.

---

## 4. Infrastructure Change & Deployment Workflow

Infrastructure changes are introduced through GitHub Pull Requests.

The standard workflow is:

```text
Infrastructure Change
        ↓
Feature Branch
        ↓
Pull Request
        ↓
Terraform Validation
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
        ↓
Success / Remediation
```

GitHub Actions handles deterministic technical checks such as formatting, validation, and Terraform planning.

Human engineers remain responsible for reviewing and authorizing infrastructure changes.

### Environment Deployment Controls

| Environment | Deployment Control |
|---|---|
| Development | Automated |
| Pre-production | Additional review |
| Production | Two-step authorization |

Production requires:

1. Senior Engineer Review
2. Authorized Engineer Approval
3. Terraform Apply
4. Post-Deployment Verification

### Deployment Workflow Diagram

![Infrastructure Change and Deployment Workflow](diagrams/infrastructure-change-deployment-workflow.png)

[SVG version](diagrams/infrastructure-change-deployment-workflow.svg)

---

## 5. Security & Access Boundaries

The architecture uses multiple independent boundaries to reduce infrastructure risk.

### Environment Boundary

Each environment has its own GCP project.

This prevents an infrastructure operation targeting one environment from directly modifying resources in another environment.

### State Boundary

Production Terraform state is stored separately from non-production state.

This prevents Production state from sharing the same storage boundary as Development or Pre-production.

### Identity Boundary

Each environment uses a dedicated Terraform service account.

This limits the permissions available to Terraform operations within each environment.

### Authorization Boundary

Production deployment requires additional human authorization beyond the standard Development and Pre-production workflow.

### Core Principle

> **The architecture is designed so that making an infrastructure mistake has a limited blast radius wherever practical.**
