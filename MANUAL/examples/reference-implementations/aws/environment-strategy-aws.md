# AWS Environment Strategy Reference Implementation

**AOSD Level 2 Example - AWS Platform Environment Architecture**

> **Note**: This is a reference implementation showing how AOSD environment isolation principles can be implemented on AWS. It demonstrates secure access patterns, EC2-based development, and SSM-based access controls. Adapt these patterns for your specific cloud platform and security requirements.

---

## 1. Purpose

This document defines an **environment architecture**, **access controls**, and **deployment flow** for AWS-based AOSD projects. It is designed to protect production systems, ensure reproducibility, and support workflows where AI agents generate or modify code.

---

## 2. AWS Environment Model Overview

This implementation uses multiple isolated environments with strict separation of duties.

| Component | Purpose | Source of Truth? | Access Method |
|----------|---------|------------------|---------------|
| **Dev EC2** | Code authoring, builds, Dev deployments | **Yes - all repos live here** | AWS **SSM Session Manager + MFA**, no SSH |
| **Prod EC2** | Controlled deployment box for Staging & Prod | Yes (read-only clone) | SSM + MFA |
| **Laptop** | Thin client (VS Code Remote), browser, Playwright tests | **No code stored locally** | Local OS + browser; no AWS secrets |
| **Dev Environment** | Experimental deployments, AI-assisted development | Deployed from Dev EC2 | API keys with strict throttles |
| **Staging Environment** *(future)* | Pre-prod validation, smoke tests, E2E regression | Deployed from Prod EC2 | Limited test identities |
| **Prod Environment** | Real tenants, production workloads | Deployed from Prod EC2 | Strong IAM policies |

**Key Principle:**
> Code never resides on the laptop. It lives on secure development environments with SSM + MFA enforced.

---

## 3. AWS Access Control (SSM-Only Pattern)

Strict rules:

- No SSH access to EC2 instances
- No inbound ports opened on EC2 instances
- All access via **AWS Systems Manager Session Manager**
- All sessions require **MFA**
- All sessions are **fully logged** (CloudTrail + SSM Logs)
- No long-lived credentials allowed
- No AWS secrets stored on any laptop or workstation

**AI Guardrail:**
AI assistants must **never** propose SSH commands, open ports, or store secrets on local machines.

---

## 4. AWS Development Topology

### 4.1 Dev EC2 - Primary Development Workstation

All code generation and editing happens here:

- Repositories cloned here only
- AI coding assistants interact with this filesystem
- `make test-fast`, `make test`, `make deploy` run here
- CDK builds and deployments to Dev happen here
- Safe for breaking changes

### 4.2 Prod EC2 - Controlled Deployment Box

Used for:

- Full test suite execution with `ENV=prod`
- `make security-pre-deploy` checks
- Deployments to:
  - Staging (future)
  - Prod

No feature development happens here.

### 4.3 Laptop - Thin Client

Laptop is used for:

- VS Code Remote to Dev EC2
- SSM terminal to Dev EC2 or Prod EC2
- Running Playwright test suites
- Browser-based QA

Laptop **never** stores:

- Production code
- AWS credentials
- Tenant data
- Secrets or tokens

---

## 5. AWS Deployment Environments

### 5.1 Dev

- Feature development
- AI refactoring
- Safe for breaking changes
- Playwright integration testing
- Synthetic tenants only

### 5.2 Staging (Planned)

- Pre-production environment
- Mirrors Prod configuration
- Safe place for:
  - Schema migrations
  - End-to-end regression tests
  - Deployment rehearsals

### 5.3 Prod

- Serves real customers
- All deployments come from Prod EC2
- Requires:
  - Passing full test suite
  - Passing security checks
  - Tag-based release workflow

---

## 6. AWS Promotion Workflow

### 6.1 Dev to Staging to Prod

**Step 1 - Dev Development**
- Create feature branch
- TodoWrite planning
- AI agent generates/updates code
- Fast tests: `make test-fast`
- Deploy to Dev: `ENV=dev make deploy`
- Playwright against Dev

**Step 2 - Merge to Main**
- PR created
- CI runs full test suite
- Merge only after passing

**Step 3 - Staging Deployment (Future)**
- Prod EC2 deploys: `ENV=stg make deploy`
- Full Playwright suite
- Manual product-owner UAT

**Step 4 - Prod Deployment**
- Tag release (`vX.Y.Z`)
- Prod EC2:
  - `ENV=prod make test`
  - `ENV=prod make security-pre-deploy`
  - `ENV=prod make deploy`
- Smoke tests via Playwright

---

## 7. AWS Data Isolation Rules

- Dev: synthetic data only
- Staging: sanitized / synthetic copies
- Prod: real data, fully protected
- No cross-environment data copying
- No exporting prod data to local machines

**AI Guardrail:**
AI must refuse suggestions to copy prod data to Dev or laptop.

---

## 8. AWS Playwright E2E Testing Strategy

Executed from laptop:

- `playwright.dev.config.ts`
- `playwright.stg.config.ts`
- `playwright.prod.config.ts`

Prod tests must be **non-destructive** (only read flows).

---

## 9. AWS-Specific Non-Negotiable Rules

- All code lives on Dev EC2
- SSM + MFA only
- No SSH, no open ports
- Laptop is never a source-of-truth
- Prod deployments only from Prod EC2
- Strict separation between Dev / Staging / Prod
- AI tools must follow safe deployment patterns
- No reproduction of sensitive data into lower environments

---

## 10. Future Enhancements

- Automated promotion pipelines
- Staging environment automation
- AI-assisted environment drift detection
- Automated Playwright suites across all environments

---

## Related Documents

- [AOSD Framework](../../../AOSD_FRAMEWORK.md) - Core methodology
- [Environment Strategy Principles](../../../ENVIRONMENT_STRATEGY.md) - Generalized principles
- [AWS Factory Pattern Example](./aws-factory-pattern-example.md) - AWS service client patterns

---

**End of AWS Environment Strategy Reference Implementation**
