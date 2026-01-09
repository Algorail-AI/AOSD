# Environment Strategy

**AOSD Framework - Environment Isolation Principles**

---

## 1. Purpose

This document defines the **environment architecture principles**, **access control patterns**, and **deployment flow guidelines** for AOSD projects. These principles are designed to protect production systems, ensure reproducibility, and support workflows where AI agents generate or modify code.

These principles are **platform-agnostic** and apply to any cloud provider or infrastructure setup.

---

## 2. Core Environment Model

AOSD uses multiple isolated environments with strict separation of duties.

### 2.1 Environment Types

| Environment Type | Purpose | Key Characteristics |
|-----------------|---------|---------------------|
| **Development Environment** | Code authoring, builds, dev deployments | Source of truth for code; AI agents operate here |
| **Production Deployment Environment** | Controlled deployment to staging/prod | Read-only access to code; deployment execution only |
| **Client Environment** | Thin client access, test execution | No code storage; remote access only |
| **Dev Target** | Experimental deployments | Safe for breaking changes; synthetic data only |
| **Staging Target** | Pre-production validation | Mirrors production configuration |
| **Production Target** | Real workloads | Strict access controls; real data |

### 2.2 Key Principle

> Code never resides on client machines. It lives on secure development environments with strong authentication enforced.

---

## 3. Access Control Principles

### 3.1 Secure Access Pattern

Strict rules for environment access:

- **No direct shell access** (SSH) to critical infrastructure
- **No open inbound ports** on development machines
- All access via **managed session services** with audit trails
- All sessions require **multi-factor authentication (MFA)**
- All sessions are **fully logged** for audit purposes
- **No long-lived credentials** on any workstation
- **No secrets stored** on client machines

### 3.2 AI Guardrails

AI assistants must **never**:
- Propose direct shell access commands
- Suggest opening network ports
- Store secrets on local machines
- Bypass authentication mechanisms

---

## 4. Development Topology

### 4.1 Primary Development Environment

All code generation and editing happens here:

- Repositories are cloned and maintained here
- AI coding assistants interact with this filesystem
- Build and test commands execute here
- Deployments to development targets happen here
- Safe for breaking changes

**Implementation Options**:
- Cloud-based development instances (AWS EC2, Azure VMs, GCP Compute)
- Container-based development environments
- Managed development platforms (GitHub Codespaces, GitPod)

### 4.2 Production Deployment Environment

Used exclusively for:

- Full test suite execution before production deployment
- Security validation checks
- Deployments to staging and production

No feature development happens here.

### 4.3 Client Environment

The client (laptop/desktop) is used for:

- Remote access to development environments
- Running end-to-end test suites
- Browser-based validation and QA

Client machines **never** store:

- Source code
- Cloud credentials
- Tenant/customer data
- Secrets or tokens

---

## 5. Deployment Environment Tiers

### 5.1 Development Target

- Feature development and experimentation
- AI-assisted refactoring
- Safe for breaking changes
- Integration testing with synthetic data
- Synthetic tenants only

### 5.2 Staging Target

- Pre-production validation environment
- Mirrors production configuration
- Safe place for:
  - Schema migrations
  - End-to-end regression tests
  - Deployment rehearsals

### 5.3 Production Target

- Serves real customers/users
- All deployments come from production deployment environment
- Requires:
  - Passing full test suite
  - Passing security checks
  - Version-tagged releases

---

## 6. Promotion Workflow

### 6.1 Standard Promotion Path: Dev to Staging to Prod

**Step 1 - Development**
- Create feature branch
- TodoWrite planning
- AI agent generates/updates code
- Run fast tests
- Deploy to development target
- Run integration tests

**Step 2 - Merge to Main**
- Pull request created
- CI runs full test suite
- Merge only after passing checks

**Step 3 - Staging Deployment**
- Deploy from production deployment environment
- Full end-to-end test suite
- Manual validation/UAT

**Step 4 - Production Deployment**
- Tag release with semantic version
- Run full test suite
- Run security pre-deployment checks
- Deploy to production
- Run smoke tests (non-destructive only)

---

## 7. Data Isolation Rules

- **Development**: Synthetic data only
- **Staging**: Sanitized or synthetic copies
- **Production**: Real data, fully protected
- **No cross-environment data copying**
- **No exporting production data** to development or client machines

**AI Guardrail:**
AI must refuse any suggestion to copy production data to development or client environments.

---

## 8. End-to-End Testing Strategy

Tests are executed from the client environment against different targets:

- Development configuration for feature testing
- Staging configuration for pre-production validation
- Production configuration for smoke tests only

**Critical Rule**: Production tests must be **non-destructive** (read-only operations).

---

## 9. Non-Negotiable Environment Rules

1. All source code lives in development environment
2. Strong authentication (MFA) required for all access
3. No direct shell access to infrastructure
4. Client machines never store sensitive data
5. Production deployments only from production deployment environment
6. Strict separation between Dev / Staging / Prod
7. AI tools must follow safe deployment patterns
8. No reproduction of production data into lower environments

---

## 10. Implementation Considerations

When implementing these principles for your platform:

### Cloud Platform Choices
- **AWS**: SSM Session Manager, EC2, IAM
- **Azure**: Bastion, VMs, Azure AD
- **GCP**: IAP, Compute Engine, Cloud Identity

### Development Environment Options
- Cloud VMs with remote IDE access
- Container-based development
- Managed development platforms

### Access Control Implementation
- Session management services
- Identity and access management
- Audit logging and monitoring

---

## 11. Related Documents

- [AOSD Framework](./AOSD_FRAMEWORK.md) - Core methodology (Principle 3: Environment Isolation)
- [Development Workflow](./DEVELOPMENT_WORKFLOW.md) - Workflow principles
- [AWS Environment Strategy Example](./examples/reference-implementations/aws/environment-strategy-aws.md) - AWS-specific implementation

---

**End of ENVIRONMENT_STRATEGY.md**
