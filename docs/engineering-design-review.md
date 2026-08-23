# Engineering Design Review (EDR)

## Document Control

| Field | Value |
|---|---|
| Status | Draft |
| Version | 0.2 |
| Document Owner | Amruthavalli Chivukula |
| Created | 03-08-2026 |
| Last Updated | 23-08-2026 |

---

## 1. Executive Summary

**Infrastructure Foundations** is a Terraform-based Infrastructure as Code capability designed for a growing startup with approximately 30–100 employees whose cloud infrastructure is beginning to outgrow manual provisioning and management practices.

The solution establishes a reproducible and standardized foundation for Google Cloud infrastructure across Development, Pre-production, and Production environments. Foundational infrastructure will be provisioned and managed through version-controlled Terraform configuration rather than relying on manual infrastructure changes.

The capability is designed around a controlled engineering workflow in which infrastructure changes are proposed, validated, reviewed, approved, applied, and verified through a repeatable process.

The architecture emphasizes environment isolation, controlled access, remote state management, auditable change history, and progressively stronger controls as changes approach Production.

The primary objective of V1 is not to build a comprehensive enterprise infrastructure platform, but to demonstrate how Infrastructure as Code can provide:

- Reproducibility
- Standardization
- Accountability
- Controlled change
- Reduced configuration drift
- Operational efficiency
- Reduced infrastructure blast radius

---

## 2. Problem Statement

As the startup grows, manually provisioned cloud infrastructure becomes increasingly difficult to maintain consistently across environments.

Resources may be configured differently depending on who provisioned them, infrastructure changes may lack a reliable audit trail, and reproducing an environment after failure or for a new development requirement can require significant manual effort.

These practices introduce several operational risks:

- Configuration drift between environments
- Accidental over- or under-provisioning of resources
- Limited visibility into who changed infrastructure and why
- Dependence on individual engineers with knowledge of manual provisioning procedures
- Increased effort when creating or reproducing environments
- Greater difficulty maintaining consistency as the engineering organization grows
- Increased risk of accidental Production changes

The problem is therefore not simply the absence of automation.

The underlying problem is the absence of a **consistent, version-controlled, reproducible, and controlled method for defining and managing infrastructure**.

---

## 3. Engineering Capability

Infrastructure Foundations demonstrates how a growing engineering organization can transition from manually managed cloud infrastructure to a controlled Infrastructure as Code operating model.

The capability provides:

- Declarative infrastructure definitions using Terraform
- Reusable Terraform modules
- Environment-specific Terraform configurations
- Separate GCP projects for Development, Pre-production, and Production
- Remote Terraform state management
- Environment-specific Terraform execution identities
- Pull-request-based infrastructure changes
- Automated Terraform validation and planning
- Human review and authorization
- Environment-specific deployment controls
- Post-deployment verification
- Rollback and remediation through known-good Terraform configuration

The design intentionally favors **making incorrect actions difficult to perform** rather than relying solely on engineers to avoid mistakes.

---

## 4. Objectives

The solution aims to:

1. Standardize infrastructure provisioning across environments.
2. Make infrastructure reproducible through version-controlled configuration.
3. Reduce configuration drift between environments.
4. Provide an auditable history of infrastructure changes.
5. Reduce dependence on individual engineers for infrastructure provisioning.
6. Enable controlled infrastructure changes through review and approval.
7. Improve onboarding and environment recreation.
8. Establish clear separation between Development, Pre-production, and Production.
9. Reduce the potential blast radius of infrastructure mistakes.
10. Establish a foundation that can evolve into a more comprehensive platform engineering capability.

### Core Design Principle

> **Optimize for isolation, controlled change, and reduced blast radius rather than maximum cost efficiency or maximum automation.**

---

## 5. Success Criteria

V1 will be considered successful when:

- Foundational infrastructure can be provisioned from Terraform configuration rather than manually recreated.
- Infrastructure configuration is version-controlled.
- Development, Pre-production, and Production have clearly separated infrastructure boundaries.
- Each environment exists in a separate GCP project.
- Terraform state is stored remotely rather than on an individual engineer's machine.
- Production Terraform state has a dedicated storage boundary.
- Infrastructure changes can be reviewed before being applied.
- Terraform can generate a plan showing proposed infrastructure changes.
- Infrastructure changes have an identifiable history through the GitHub workflow.
- Terraform operations use environment-specific service accounts.
- Development changes can be deployed through an automated workflow.
- Pre-production changes require an additional deployment review.
- Production changes require two-step authorization.
- Post-deployment verification is performed.
- A failed or inappropriate change can be reverted to a known-good Terraform configuration and verified.
- A new engineer can understand and reproduce the infrastructure using the repository documentation and Terraform configuration.

---

## 6. Scope

### In Scope — V1

#### Cloud Provider

- Google Cloud Platform

#### Infrastructure as Code

- Terraform
- Reusable Terraform modules
- Environment-specific root configurations

#### Environments

- Development
- Pre-production
- Production

Each environment will use a separate GCP project under the same GCP organization.

#### Infrastructure Resources

- Compute
- Storage
- Minimal IAM required for the capability
- Environment-specific Terraform service accounts

#### Operational Capabilities

- Version-controlled infrastructure configuration
- Environment isolation
- Remote Terraform state
- State locking
- Terraform plan/apply workflow
- Pull-request-based change review
- GitHub Actions automation
- Environment-specific deployment controls
- Post-deployment verification
- Rollback/remediation approach
- Auditable change history

### Explicitly Out of Scope — V1

The following will deliberately not be implemented unless required to demonstrate the core capability:

- Kubernetes
- Docker/container orchestration
- Complex network architecture
- Advanced network segmentation
- DNS management
- Monitoring dashboards
- Comprehensive observability
- Enterprise-scale IAM architecture
- Multi-cloud support
- Agentic AI
- Complex policy-as-code systems
- Large-scale enterprise governance
- Sophisticated automated Production approval orchestration

---

## 7. Technology Stack & Decisions

| Decision | Selected Technology | Primary Reason |
|---|---|---|
| Cloud | Google Cloud Platform | Project direction and GCP capability focus |
| Infrastructure as Code | Terraform | Mature ecosystem, HCL, provider support |
| Version Control | GitHub | Pull-request workflow and repository integration |
| CI/CD | GitHub Actions | Lightweight automation integrated with GitHub |
| State Storage | Google Cloud Storage | Native GCP integration and simple V1 architecture |
| Environment Isolation | Three GCP projects | Reduced blast radius and stronger Production isolation |
| Execution Identity | Environment-specific service accounts | Least privilege and clearer operational accountability |

### Decision Principle

> **Tools are selected because they solve a requirement in the system, not because the technology itself is the objective.**

---

## 8. Proposed Architecture

The solution uses Terraform as the Infrastructure as Code layer between version-controlled configuration and Google Cloud infrastructure.

Engineers propose infrastructure changes through GitHub. Terraform configuration is validated and planned through GitHub Actions, reviewed by engineers, and then applied according to the deployment controls of the target environment.

The architecture consists of:

- GitHub for version control and collaboration
- Terraform for infrastructure definition and execution
- Google Cloud Provider for communication with GCP
- Remote GCS state storage
- Separate GCP projects for each environment
- Environment-specific Terraform service accounts
- Environment-specific deployment controls

Production is treated as a separate trust boundary from non-production.

Detailed architecture, environment boundaries, Terraform structure, state management, access controls, deployment workflow, and recovery mechanisms are documented separately in [`architecture.md`](architecture.md).

### Architecture Diagrams

The repository contains two supporting architecture diagrams:

- **Architecture Overview — Terraform-Based Infrastructure Management**
- **Infrastructure Change & Deployment Workflow**

These diagrams provide the visual representation of the system architecture and infrastructure change lifecycle.

---

## 9. Repository Structure

The repository is organized to separate infrastructure implementation from supporting engineering documentation.

The repository will contain:

- Terraform configuration
- Reusable Terraform modules
- Environment-specific configurations
- GitHub Actions workflows
- Architecture documentation
- Engineering Design Review documentation
- Architecture diagrams
- Supporting project documentation

The detailed repository structure will evolve alongside implementation while maintaining clear separation between reusable infrastructure components and environment-specific configuration.

---

## 10. Risks & Trade-offs

### Three GCP Projects Increase Administrative Overhead

**Trade-off:** Additional projects, permissions, and configuration boundaries must be managed.

**Accepted because:** Environment isolation and reduced Production blast radius are more important than minimizing project count.

### Production Deployment Is Less Automated

**Trade-off:** Production changes require additional human intervention.

**Accepted because:** Production has substantially higher potential impact and therefore warrants stronger authorization controls.

### Two-Step Production Authorization Slows Deployment

**Trade-off:** Production changes cannot be deployed as quickly as Development changes.

**Accepted because:** Production infrastructure has a higher potential blast radius, making stronger authorization more valuable than maximum deployment speed.

### Separate Production State Storage Increases Complexity

**Trade-off:** Additional state infrastructure must be maintained.

**Accepted because:** Production Terraform state is treated as a separate trust boundary.

### GitHub Actions Adds CI/CD Configuration

**Trade-off:** The repository requires additional workflow configuration.

**Accepted because:** GitHub Actions automates deterministic Terraform validation, planning, and environment-specific execution without requiring a complex deployment platform.

### IAM Is Deliberately Limited in V1

The project will implement enough IAM to demonstrate environment isolation, service identities, and least-privilege principles.

It will not attempt to model an enterprise-scale IAM architecture.

---

## 11. Future Improvements

Potential future capabilities include:

- More environments
- More granular IAM
- Automated Production deployment with stronger approval gates
- Integration with formal change-management systems
- Automated policy validation
- Infrastructure security scanning
- Monitoring and observability
- Drift detection and remediation
- Secret-management integration
- Networking and DNS provisioning
- Containerization
- Kubernetes
- More sophisticated rollback mechanisms
- Advanced governance and compliance controls

These capabilities are deliberately outside the scope of V1.

---

## 12. Integration with CloudOps Lab

Infrastructure Foundations is intended to serve as a foundational capability within the broader **CloudOps Lab** initiative.

The project demonstrates the Infrastructure as Code and infrastructure governance layer that can support later platform engineering capabilities.

Future CloudOps Lab development may build upon this foundation by introducing additional infrastructure services, observability, security controls, networking, application deployment, and broader platform automation.

The project therefore serves both as an independent engineering capability and as a building block toward the larger CloudOps Lab platform.
