# Application AOSD Documentation
**Level 3: Application-Specific AOSD Documentation**
**Copyright Algorail AI, LLC 2025**

---

> **Instructions**: Copy this template to your application's `AOSD/AOSD.md` file and fill in the sections below. Delete this instruction block when done.

---

# 🎯 Application Overview

## Application Name
**[Your application name]**

## Purpose
[1-3 sentences describing what this application does and who it serves]

## Orchestration Profile
**Profile**: [Link to your Level 2 Orchestration Profile repo]
**Version**: [Commit hash or version for reproducibility]

See [ORCHESTRATION_PROFILE.md](./ORCHESTRATION_PROFILE.md) for details and any app-specific overrides.

## Application Status
- **Started**: [YYYY-MM-DD]
- **Current Phase**: [Alpha / Beta / Production / Maintenance]
- **Team Size**: [Number of developers]

---

# 🏛️ Architecture Overview

## High-Level Architecture
[Brief description of your application's architecture]

**Key Technologies**:
- Frontend: [Technology]
- Backend: [Technology]
- Data: [Technology]
- Auth: [Technology]
- Cloud Platform: [AWS / Azure / GCP / etc.]

## System Diagram
[Link to architecture diagram or embed if using mermaid]

## Key Components

### [Component Name 1]
- **Purpose**: [What it does]
- **Technology**: [Specific tech]
- **Location**: [`path/to/component`]
- **Patterns Used**: [Reference to Level 2 patterns]

### [Component Name 2]
- **Purpose**: [What it does]
- **Technology**: [Specific tech]
- **Location**: [`path/to/component`]

[Add more components as needed]

---

# 🔒 Application-Specific Invariants

> These are non-negotiable rules specific to THIS application that extend your Orchestration Profile's invariants.

## Invariant 1: [Name]

**Rule**: [Clear, concise statement of the invariant]

**Rationale**: [Why this rule exists for this application]

**AI Guardrails**:
- AI must: [Specific requirement 1], [Specific requirement 2]
- AI must not: [Specific prohibition 1], [Specific prohibition 2]

**Validation**: [How this is tested - specific test file or CI check]

**Examples**:
- ✅ Good: [Code example that follows this invariant]
- ❌ Bad: [Code example that violates this invariant]

---

## Invariant 2: [Name]

[Same structure as above]

---

[Add more invariants as needed. If you have none yet, that's okay - delete this section or note "No application-specific invariants yet."]

---

# 📐 Application-Specific Patterns

> Patterns unique to this application that extend or specialize your Orchestration Profile patterns.

## Pattern 1: [Name]

**Problem**: [What problem does this solve in this application?]

**Solution**: [High-level approach]

**Implementation**:
- **Location**: [`path/to/implementation`]
- **Key Files**: [`file1.py`, `file2.py`]
- **Example**: [Code snippet or link]

**References**: [Link to Level 2 pattern if this extends one]

---

## Pattern 2: [Name]

[Same structure]

---

[Add more patterns as needed. Delete section if no app-specific patterns exist yet.]

---

# 📊 Data Model

## Primary Entities

### [Entity Name 1]
- **Description**: [What this entity represents]
- **Storage**: [DynamoDB table / PostgreSQL table / etc. - be specific]
- **Table/Collection Name**: [`actual-name`]
- **Key Pattern**: [Partition key: `PATTERN`, Sort key: `PATTERN`]
- **Multi-Tenancy**: [How tenant isolation is enforced]
- **Access Patterns**:
  1. [Query description]: [How it's done]
  2. [Query description]: [How it's done]

### [Entity Name 2]
[Same structure]

---

## Entity Relationships
[Describe key relationships - how entities connect]

**Diagram** (optional):
```mermaid
[Add mermaid diagram if helpful]
```

---

# 🔑 Authentication & Authorization

## Authentication
**Method**: [Cognito / Auth0 / Custom JWT / etc.]
**Implementation**: [`path/to/auth/code`]

## User Types/Roles
| Role | Description | Permissions |
|------|-------------|-------------|
| [Role 1] | [Description] | [Can do X, Y, Z] |
| [Role 2] | [Description] | [Can do A, B, C] |

## Authorization Enforcement
- **API Gateway Layer**: [How enforced]
- **Lambda/Handler Layer**: [How enforced]
- **Data Layer**: [How enforced]

## Multi-Tenancy
**Tenant Isolation Strategy**: [How tenants are isolated]
**Tenant ID Validation**: [Where and how validated]

---

# 🧪 Testing Strategy

## Fast Tests (< 1s)
- **Location**: [`tests/unit/`]
- **Coverage**: [What's tested - pure functions, business logic, etc.]
- **Run Command**: `[exact command]`
- **Current Count**: [~XX tests]

## Medium Tests (< 10s)
- **Location**: [`tests/integration/`]
- **Coverage**: [What's tested - API endpoints, service integration, etc.]
- **Run Command**: `[exact command]`
- **Current Count**: [~XX tests]

## Slow Tests (< 5min)
- **Location**: [`tests/e2e/`]
- **Coverage**: [What's tested - end-to-end workflows, real AWS, etc.]
- **Run Command**: `[exact command]`
- **Current Count**: [~XX tests]

## Special Testing Requirements
[Any unique testing needs for this application]

Examples:
- Compliance testing for [regulation]
- Load testing for [feature]
- Security scanning with [tool]

---

# 🚀 Deployment

## Environments

| Environment | URL | Purpose | Data | Deployment Trigger |
|-------------|-----|---------|------|-------------------|
| **Dev** | [URL] | Active development | Test data | Merge to `dev` branch |
| **Staging** | [URL] | Pre-production validation | Anonymized prod data | Merge to `main` branch |
| **Production** | [URL] | Live application | Real user data | Manual promotion |

## Deployment Process

1. [Step 1 - e.g., "Merge PR to dev branch"]
2. [Step 2 - e.g., "CI runs tests"]
3. [Step 3 - e.g., "Auto-deploy to Dev"]
4. [Step 4 - e.g., "Create release PR to main"]
5. [Step 5 - e.g., "Manual approval + deploy to Prod"]

## Infrastructure as Code
- **Tool**: [CDK / Terraform / CloudFormation]
- **Location**: [`infra/` or `infrastructure/`]
- **Main Stacks**: [List key IaC modules]

## Rollback Procedure
[How to rollback a bad deployment]

---

# 🤖 AI Agent Context

## Context Nuggets

This application uses Context Nuggets for just-in-time (JIT) AI context loading.

**Location**: `AOSD/context/`

**Available Nuggets**:
| Nugget | Scope | Load When |
|--------|-------|-----------|
| [`auth-flow.md`](./context/auth-flow.md) | Authentication | Working on auth-related code |
| [`data-model.md`](./context/data-model.md) | Database entities | Modifying data layer |
| [Add more...] | | |

*See [AOSD_FOLDER_STRUCTURE.md](https://github.com/Algorail-AI/AOSD/blob/main/TEMPLATES/AOSD_FOLDER_STRUCTURE.md#aosdcontext-context-nuggets) for nugget creation standards.*

## Critical Files

AI agents working on this application must be familiar with:

| File | Purpose | Notes |
|------|---------|-------|
| [`path/to/file1`] | [Purpose] | [Critical patterns or gotchas] |
| [`path/to/file2`] | [Purpose] | [Critical patterns or gotchas] |

## Directory Structure
```
src/
├── common/          # [Shared utilities]
├── services/        # [Business logic]
├── lambdas/         # [Handler functions]
├── models/          # [Data models]
└── [other dirs]     # [Purpose]
```

## Common AI Agent Tasks

### Adding a New API Endpoint
1. [Step-by-step process specific to this app]
2. [Which files to modify]
3. [Testing requirements]
4. [Deployment considerations]

### Adding a New Database Entity
1. [Process for this app]
2. [Key pattern to follow]
3. [Migration requirements]

### Refactoring Existing Code
**Guidelines**:
- [What must be preserved]
- [What can be changed]
- [Testing requirements for refactoring]

---

# 📝 Architecture Decisions

> Major architectural decisions are documented as ADRs in `AOSD/decisions/`

## Recent Decisions
- [ADR-001](./decisions/001-database-choice.md): [Short title]
- [ADR-002](./decisions/002-auth-strategy.md): [Short title]
- [ADR-003](./decisions/003-ai-feature.md): [Short title]

See [decisions/](./decisions/) folder for complete list.

---

# 🔧 Application-Specific Workflows

## Development Workflow
[Any deviations from your Orchestration Profile's standard workflow]

## Slice-Based Development
- **Typical Slice Size**: [1-3 days]
- **Slice Specs Location**: [`AOSD/slices/`]
- **Recent Slices**:
  - [2025-01-15: User Authentication](./slices/2025-01-15-user-authentication.md)
  - [2025-01-20: Dashboard UI](./slices/2025-01-20-dashboard-ui.md)

## Code Review Process
[Application-specific code review requirements]

---

# 📊 Monitoring & Operations

## Key Metrics
| Metric | Target | Alert Threshold | Dashboard |
|--------|--------|----------------|-----------|
| API Latency (p95) | < 200ms | > 500ms | [Link] |
| Error Rate | < 0.1% | > 1% | [Link] |
| [Other metric] | [Target] | [Threshold] | [Link] |

## Logging
- **Tool**: [CloudWatch / Datadog / etc.]
- **Log Retention**: [Duration]
- **PII Policy**: [What must not be logged]

## Alerting
- **Critical Alerts**: [What requires immediate response]
- **Warning Alerts**: [What to monitor]
- **On-Call**: [Process if applicable]

---

# 💰 Cost Management

## Primary Cost Drivers
[What are the main costs?]

Example:
- Lambda invocations: ~$XX/month
- Database (DynamoDB/RDS): ~$XX/month
- API Gateway: ~$XX/month
- AI Model Usage: ~$XX/month
- **Total**: ~$XX/month

## Cost Optimization Strategies
[Application-specific cost optimizations in place]

---

# 🔐 Security & Compliance

## Compliance Requirements
[Any regulatory requirements]
- [ ] [Regulation 1 - e.g., HIPAA]
- [ ] [Regulation 2 - e.g., GDPR]
- [ ] [Regulation 3 - e.g., SOC 2]

## Security Controls
- Data at rest: [Encryption method]
- Data in transit: [TLS version]
- Secrets management: [Tool/service]
- Key rotation: [Policy]

## Security Contacts
- **Security Lead**: [Name]
- **Compliance Officer**: [Name]

---

# 📚 Additional Resources

## Documentation
- [Architecture Diagrams](link)
- [API Documentation](link)
- [User Guides](link)

## Related Repositories
- [Frontend Repo](link) (if separate)
- [Shared Libraries](link)

## Team Contacts
- **Product Owner**: [Name]
- **Tech Lead**: [Name]
- **AOSD Orchestrator**: [Name]

---

# 🎯 Quick Start for New Developers

**Prerequisites**:
- [Requirement 1 - e.g., Node.js 18+]
- [Requirement 2 - e.g., AWS CLI configured]
- [Requirement 3 - e.g., Python 3.11+]

**Setup**:
```bash
# Clone repository
git clone [repo-url]

# Install dependencies
[command]

# Set up environment
[command]

# Run tests
[command]

# Start local development
[command]
```

**First Task Suggestions**:
1. [Read AOSD documentation (this file)]
2. [Run test suite to verify setup]
3. [Try simple code change with test]
4. [Review recent slices for context]

**Key Reading**:
- This file (AOSD.md)
- [Orchestration Profile](./ORCHESTRATION_PROFILE.md)
- [Recent ADRs](./decisions/)
- [Application README](../README.md)

---

# 🔄 Document Maintenance

**Last Updated**: [YYYY-MM-DD]
**Updated By**: [Name]
**Update Frequency**: [Monthly / As needed / etc.]

**Change History**:
- YYYY-MM-DD: [What changed]
- YYYY-MM-DD: [What changed]

---

**End of Application AOSD Documentation**
