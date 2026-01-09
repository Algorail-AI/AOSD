# AOSD Implementation Guide
**Documenting Your Orchestration Profile (Level 2)**
**Copyright Algorail AI, LLC 2025**

<!--
AI INGESTION GUIDELINES:
- This template creates Level 2 Orchestration Profiles
- AI agents should understand the three-level architecture first
- Each section uses ADR format for decision documentation
- Cross-references to AOSD_FRAMEWORK.md provide methodology context
- See AI_GUIDE.md for profile creation patterns
-->

---

# 📘 Purpose

This template helps you document your **Orchestration Profile** (Level 2) when adopting AOSD methodology. The AOSD Framework defines **what** to do; this guide helps you document **how** you're doing it in your environment.

**Orchestration Profile** = Your specific choices for cloud platform, AI agents, tools, environments, and organizational patterns.

---

# 📋 How to Use This Template

1. **Create your own Level 2 repository** (e.g., `your-company-aosd` or `your-name-aosd`)
2. **Copy this template** to your repository's main documentation
3. **Fill in each section** with your specific choices using ADR format
4. **Keep updated** as your implementation evolves
5. **Reference from applications** - your Level 3 apps will point to this profile

---

# 📁 Recommended Repository Structure

This section provides a **recommended** (not required) folder structure for organizations adopting AOSD. The goal is to establish a clear boundary between upstream AOSD and your organization's extensions.

## Core Invariant

> **Upstream AOSD is never modified by adopting organizations.**

Extensions are always additive. If upstream needs to change, contribute back via PR to the AOSD Framework repository.

## Recommended Structure

```
your-org-aosd/
├── AOSD/                    # Upstream (git submodule, subtree, or copy)
│   └── [untouched]          # Never modify files in this directory
├── ORG_PROFILE/             # Organization-level L2
│   ├── ORCHESTRATION_PROFILE.md  # Your profile (from this template)
│   ├── INVARIANTS.md        # Org-wide constraints
│   ├── PATTERNS/            # Org-approved patterns
│   │   └── [pattern-name].md
│   └── TEMPLATES/           # Org-specific templates
│       └── [template-name].md
├── PROJECTS/                # Project-level L3 (optional)
│   └── <project-name>/      # Project-specific documentation
│       ├── invariants.md    # Project-specific constraints
│       └── examples/        # Project-specific implementations
├── README.md                # Overview pointing to profile
└── CHANGELOG.md             # Version history of your profile
```

## Structure Guidelines

### Upstream AOSD Directory

The `AOSD/` directory contains the upstream AOSD Framework. Choose one inclusion method:

| Method | Pros | Cons | Best For |
|--------|------|------|----------|
| **Git submodule** | Version pinning, easy updates | Submodule complexity | Teams familiar with git submodules |
| **Git subtree** | No submodule complexity | Harder to update | Teams avoiding submodules |
| **Manual copy** | Simple, no git complexity | Manual update burden | Small teams, infrequent updates |

Regardless of method:
- **MUST NOT** modify any files within the AOSD directory
- **SHOULD** pin to a specific version tag (e.g., `v0.2.0`)
- **SHOULD** document the upstream version in your profile

### Organization Profile Directory

The `ORG_PROFILE/` directory contains your Level 2 Orchestration Profile:

| File/Folder | Purpose |
|-------------|---------|
| `ORCHESTRATION_PROFILE.md` | Your main profile (fill out this template) |
| `INVARIANTS.md` | Organization-wide non-negotiable rules |
| `PATTERNS/` | Approved patterns beyond those in upstream |
| `TEMPLATES/` | Organization-specific templates |

### Projects Directory (Optional)

The `PROJECTS/` directory is optional. Use it when:
- You maintain multiple projects sharing this profile
- Projects have unique invariants beyond org-level
- You want centralized L3 documentation

Alternatively, L3 documentation can live in each project's own repository under an `AOSD/` folder.

## Flexibility Language

This structure uses intentional language for requirements:

| Term | Meaning |
|------|---------|
| **RECOMMENDED** | The standard approach; deviate only with good reason |
| **MAY** | Optional; choose what works for your context |
| **MUST NOT** | Prohibited; violating this creates problems |

### Structure Requirements

- **RECOMMENDED**: Use the folder structure shown above
- **MAY**: Use alternate layouts (monorepo, `docs/aosd/`, nested folders) if invariants are preserved
- **MAY**: Omit `PROJECTS/` if L3 docs live in application repos
- **MUST NOT**: Modify files in the upstream AOSD directory

## Alternative Layouts

### Monorepo Layout

For organizations using a monorepo structure:

```
your-monorepo/
├── docs/
│   └── aosd/
│       ├── upstream/        # Upstream AOSD
│       ├── profile/         # Org profile (L2)
│       └── projects/        # Project docs (L3)
├── packages/
│   └── [your-apps]/
└── ...
```

### Minimal Layout

For solo developers or small teams:

```
my-aosd/
├── AOSD/                    # Upstream
├── ORCHESTRATION_PROFILE.md # Your profile (single file)
├── INVARIANTS.md            # Your invariants
└── CHANGELOG.md
```

## Why This Matters

This structure provides:

1. **Clear boundaries** - Upstream vs. your extensions are visually distinct
2. **Safe updates** - Upstream can be updated without merge conflicts
3. **Tooling compatibility** - AI agents and automation can locate artifacts reliably
4. **Contribution path** - When you improve AOSD, changes can be contributed back cleanly

---

# ✅ Decision Points Checklist

**Required decisions every Level 2 owner must make.** Use this checklist before filling out the template to ensure you've thought through all critical choices.

## Core Infrastructure Decisions

- [ ] **Cloud Platform**: Which cloud platform(s) will you use? (AWS, Azure, GCP, on-premises, hybrid)
- [ ] **Development Environment**: Where will development happen? (Cloud-based, local, hybrid, container-based)
- [ ] **Source Code Location**: Where does code live? (Cloud-only, local-permitted, both)

## AI Agent Decisions

- [ ] **Builder Agent**: Which AI fills the Builder/Implementer role? (Claude Code, Copilot, Cursor, etc.)
- [ ] **Reviewer Agent**: Which AI fills the Architect/Reviewer role? (ChatGPT, Claude, Gemini, etc.)
- [ ] **Transformer Agent**: Separate agent for transformations or same as Builder?
- [ ] **Constrained Agent**: Do you need a specialized agent for restricted environments?

## Environment & Security Decisions

- [ ] **Environment Count**: How many environments? (Dev only, Dev+Prod, Dev+Staging+Prod)
- [ ] **Access Method**: How do developers access environments? (SSH, SSM, VPN, Bastion)
- [ ] **MFA Requirement**: Where is MFA required? (Everywhere, production only, etc.)
- [ ] **API Protection**: What protects your APIs? (WAF, API Gateway, custom)

## Process Decisions

- [ ] **Branching Strategy**: Which strategy? (Feature branches, trunk-based, GitFlow)
- [ ] **Work Tracking**: Where are work items tracked? (GitHub Issues, Jira, Linear, etc.)
- [ ] **Slice Size**: What's your target slice duration? (1 day, 1-3 days, 1 week)
- [ ] **Review Requirements**: Who reviews what? (Human-only, AI-assisted, team leads)

## Testing Decisions

- [ ] **Test Tiers**: How are Fast/Medium/Slow tests defined for your context?
- [ ] **Coverage Targets**: What coverage is acceptable for each tier?
- [ ] **Test Location**: Where do tests run? (Local, CI, both)

## Cost Decisions

- [ ] **Model Tiers**: Which models for which tasks? (Cheap/Fast vs. Expensive/Capable)
- [ ] **Budget Limits**: What's the monthly AI budget? Alert thresholds?
- [ ] **Cost Tracking**: How are costs monitored? (Cloud tools, manual, third-party)

## Team Decisions

- [ ] **Team Scale**: Solo, small (2-5), medium (6-15), or enterprise (15+)?
- [ ] **Orchestrator Role**: Who fills this role? Single person or rotating?
- [ ] **Training Approach**: How will team members learn AOSD?

---

# 🔄 Common Patterns Reference

**Frequently used patterns when creating Orchestration Profiles.** These patterns address common decisions and provide starting points.

## Pattern: Single Cloud Platform

**When**: Your organization standardizes on one cloud provider.

**Decision Structure**:
- Primary Platform: [AWS/Azure/GCP]
- Secondary: None (explicitly state single-platform commitment)
- Rationale: Team expertise, cost optimization, compliance requirements

**Trade-offs**:
- ✅ Simpler operations, deeper expertise, better cost optimization
- ❌ Vendor lock-in, limited flexibility

## Pattern: Dual Agent (Builder + Reviewer)

**When**: Most teams starting with AOSD.

**Decision Structure**:
- Builder: [Claude Code / Cursor / Copilot] - day-to-day implementation
- Reviewer: [ChatGPT / Claude Web / Gemini] - architecture review, design feedback
- Transformer: Same as Builder (combined role)
- Constrained: Not applicable (no restricted environments)

**Trade-offs**:
- ✅ Clear separation of concerns, diverse perspectives
- ❌ Context switching between tools, potential inconsistency

## Pattern: GitHub-Native Workflow

**When**: Teams using GitHub as their primary platform.

**Decision Structure**:
- Work Tracking: GitHub Issues
- Branching: Feature branches from main
- Reviews: GitHub Pull Requests
- CI/CD: GitHub Actions
- Branch Naming: `{type}/{issue-number}-{description}`
- Commit Format: `{type}: {description} (#{issue-number})`

**Trade-offs**:
- ✅ Tight integration, single platform, good automation
- ❌ Limited compared to dedicated project management tools

## Pattern: Three-Environment Promotion

**When**: Production systems requiring staging validation.

**Decision Structure**:
- Development: Full access, test data, auto-deploy on merge
- Staging: Production-like, anonymized data, manual promotion
- Production: Restricted access, real data, approval required

**Promotion Flow**:
```
Dev branch → PR → merge → auto-deploy to Dev
                        → manual trigger → deploy to Staging
                        → approval gate → deploy to Production
```

**Trade-offs**:
- ✅ Thorough validation, risk reduction
- ❌ Slower deployment, environment maintenance overhead

## Pattern: Fast-First Testing

**When**: Teams prioritizing rapid feedback loops.

**Decision Structure**:
- Fast Tests (Tier 1): Run on every commit, < 30 seconds total
- Medium Tests (Tier 2): Run on PR creation, < 5 minutes
- Slow Tests (Tier 3): Run nightly or before release

**Coverage Strategy**:
- Fast: 80%+ of pure business logic
- Medium: 60%+ of integration points
- Slow: Critical user journeys only

**Trade-offs**:
- ✅ Rapid feedback, high developer productivity
- ❌ Potential gaps in integration coverage

## Pattern: Cost-Tiered Model Selection

**When**: Budget-conscious teams optimizing AI costs.

**Decision Structure**:
- Tier 1 (90% of tasks): Fast/cheap model (Haiku, GPT-3.5) for routine work
- Tier 2 (9% of tasks): Balanced model (Sonnet, GPT-4) for standard development
- Tier 3 (1% of tasks): Powerful model (Opus, GPT-4 Turbo) for complex architecture

**Selection Heuristic**:
```
If task is: code completion, simple refactor, documentation → Tier 1
If task is: feature implementation, bug investigation, code review → Tier 2
If task is: architecture design, security review, complex debugging → Tier 3
```

**Trade-offs**:
- ✅ Significant cost savings, appropriate capability matching
- ❌ Requires discipline, occasional capability mismatches

## Anti-Patterns to Avoid

### Anti-Pattern: Undefined Invariants

**Problem**: Profile documents tooling choices but no invariants.

**Why It Fails**: Without invariants, AI agents have no constraints; drift is inevitable.

**Fix**: Define at least 5-10 non-negotiable rules before proceeding.

### Anti-Pattern: Over-Documentation

**Problem**: Profile is 50+ pages covering every possible scenario.

**Why It Fails**: Too long to read, too hard to maintain, never updated.

**Fix**: Document decisions you've actually made. Add sections as needed.

### Anti-Pattern: Copy-Paste from Examples

**Problem**: Copying reference implementations without understanding.

**Why It Fails**: Examples are illustrative, not prescriptive. Context matters.

**Fix**: Understand the pattern, then adapt to your specific situation.

### Anti-Pattern: Platform Leakage

**Problem**: Mixing platform-specific details into Level 3 app documentation.

**Why It Fails**: Level 3 should only contain app-specific information.

**Fix**: Keep platform decisions in Level 2; reference from Level 3.

---

# 🏗️ Orchestration Profile Overview

## Organization/Individual Name
**[Your organization or name]**

## Profile Maintainer
**[Person or team responsible]**

## Profile Start Date
**[When you began using AOSD]**

## Profile Status
**[Active / Pilot / Archived]**

## Cloud Platform Support
**Primary**: [AWS / Azure / GCP / On-premises / Hybrid]
**Secondary** (if applicable): [Platform]

## Framework Compatibility
**AOSD Framework Version**: [e.g., 0.x.x]
**Minimum Compatible**: [e.g., 0.1.0]
**Last Tested With**: [e.g., 0.1.0]

*See AOSD Framework CHANGELOG.md for version history and migration guidance.*

---

# ☁️ Cloud Platform Selection

## Decision: Primary Cloud Platform

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Context
[Why you needed to choose a cloud platform and what factors mattered]

### Considered Options
- Option 1: [e.g., AWS] - [Brief pros/cons]
- Option 2: [e.g., Azure] - [Brief pros/cons]
- Option 3: [e.g., GCP] - [Brief pros/cons]
- Option 4: [e.g., On-premises] - [Brief pros/cons]

### Decision
**Selected**: [Your choice]

### Rationale
- **Existing expertise**: [What experience you have]
- **Cost considerations**: [Budget constraints or optimizations]
- **Compliance requirements**: [Any regulatory needs]
- **Feature availability**: [Required capabilities]
- **[Other factors]**: [Explanation]

### Consequences
**Positive**:
- [Benefit 1]
- [Benefit 2]

**Negative**:
- [Tradeoff 1]
- [Tradeoff 2]

---

# 🤖 AI Agent and Model Selection

AOSD defines four abstract agent roles that you assign your chosen AI models to. See [MODEL_ROLES.md](MODEL_ROLES.md) for detailed role definitions and [examples/reference-implementations/model-assignments/](examples/reference-implementations/model-assignments/) for an example assignment.

## Agent Role Mapping Summary

Before documenting individual decisions, summarize your role assignments:

| AOSD Role | Your Agent/Model | Notes |
|-----------|------------------|-------|
| Builder/Implementer | [e.g., Claude Code] | [Primary dev agent] |
| Architect/Reviewer | [e.g., ChatGPT GPT-4] | [Design and review] |
| Transformer/Synthesizer | [e.g., Codex / Same as Builder] | [Optional: for transformations] |
| Constrained Executor | [e.g., Local model / None] | [Optional: for restricted envs] |

---

## Decision: Builder/Implementer Agent

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Context
[Which AI agent will fill the Builder/Implementer role? See MODEL_ROLES.md Section 3 for role definition.]

### Considered Options
- **Claude Code** (Anthropic) - [Why considered]
- **GitHub Copilot** - [Why considered]
- **Cursor** - [Why considered]
- **Other IDE-integrated agents** - [Why considered]
- **Other**: [specify]

### Decision
**Selected**: [Your choice]

**Model**: [e.g., claude-sonnet-4.5]

### Rationale
- [Capability assessment]
- [Cost considerations]
- [Integration with workflow]
- [Team familiarity]

### Configuration
- **Access Method**: [Desktop app / API / VS Code extension]
- **Cost Tier**: [Free / Pro / Enterprise]
- **Usage Guidelines**: [When to use this agent]

---

## Decision: Architect/Reviewer Agent

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Context
[Which AI agent will fill the Architect/Reviewer role? See MODEL_ROLES.md Section 4 for role definition.]

### Considered Options
- **ChatGPT** (OpenAI) - [Why considered]
- **Claude** (Web/API) - [Why considered]
- **Gemini** (Google) - [Why considered]
- **Other**: [specify]

### Decision
**Selected**: [Your choice]

**Model**: [e.g., gpt-4]

### Rationale
- [Why this choice for architecture review]
- [Strengths for this role]
- [Cost considerations]

### Configuration
- **Access Method**: [Web / API / Other]
- **Cost Tier**: [Free / Plus / Enterprise]
- **Usage Guidelines**: [When to use this agent]

---

## Decision: Transformer/Synthesizer Agent (Optional)

**Status**: [Proposed / Accepted / Deprecated / Not Applicable]

**Decision Date**: [YYYY-MM-DD]

### Context
[Which AI agent will fill the Transformer/Synthesizer role? See MODEL_ROLES.md Section 5 for role definition. This role is optional - many teams use their Builder agent for transformations.]

### Considered Options
- **Same as Builder** - [Builder handles transformations]
- **Codex** (OpenAI) - [Specialized for transformations]
- **Dedicated transformation tool** - [Why considered]
- **Not needed** - [Why this role is not required]

### Decision
**Selected**: [Your choice or "Same as Builder"]

### Rationale
- [Why this choice - or why the role is combined with Builder]

### Use Cases
- [When this agent is used instead of Builder]
- [Types of transformations it handles]

---

## Decision: Constrained Executor Agent (Optional)

**Status**: [Proposed / Accepted / Deprecated / Not Applicable]

**Decision Date**: [YYYY-MM-DD]

### Context
[Which AI agent will operate in constrained environments? See MODEL_ROLES.md Section 6 for role definition. This role is optional - only needed if you have restricted environments where primary agents cannot operate.]

### Considered Options
- **Not needed** - [No constrained environments]
- **Local model** (e.g., Llama, Mistral) - [Why considered]
- **Cloud model available in restricted environment** - [Why considered]
- **Same as Builder with constraints** - [Builder with restricted prompts]

### Decision
**Selected**: [Your choice or "Not Applicable"]

### Rationale
- [Why this choice - or why the role is not needed]

### Constrained Environment Details
- **Environment(s)**: [Which environments require constrained executor]
- **Constraints**: [Context limits, security controls, etc.]
- **Prompting Pattern**: [How prompts are structured for safety]

---

## Decision: Specialist Agents (Optional)

These are domain specialists beyond the four core AOSD roles.

### Infrastructure Specialist
- **Agent**: [e.g., Amazon Q / GitHub Copilot / None]
- **Use Cases**: [When to use - e.g., CloudFormation, infrastructure code]
- **Configuration**: [Model, access method]

### Security Specialist
- **Agent**: [If applicable]
- **Use Cases**: [Security reviews, threat modeling, compliance checks]
- **Configuration**: [Details]

### Performance Specialist
- **Agent**: [If applicable]
- **Use Cases**: [Performance optimization, profiling, load testing]
- **Configuration**: [Details]

---

## Agent Command Interfaces (Optional)

Document any tool-specific command interfaces configured for your environment. These are convenience shortcuts that improve developer experience but are not part of core AOSD methodology.

### Examples by Tool

**Claude Code**:
- Slash commands (e.g., `/implement`, `/deploy-slice`, `/pr`)
- Custom hooks for workflow automation

**Cursor**:
- Custom @ commands
- Workspace-specific AI rules

**VS Code / IDE Extensions**:
- AI-assisted task definitions
- Custom keybindings for AI actions

**CLI Wrappers**:
- Wrapper scripts for common AI workflows
- Aliases that invoke AI agents

### What to Document

For each command interface:

| Field | Description |
|-------|-------------|
| **Command** | The command name or syntax |
| **Purpose** | What it does |
| **Inputs** | Required context or parameters |
| **Outputs** | What it produces |
| **HITL Level** | Trust level per Principle 8 (e.g., "Level 2: AI Drafts, Human Reviews") |

### Your Command Interfaces

*Document your configured commands below:*

| Command | Purpose | HITL Level |
|---------|---------|------------|
| [e.g., `/implement`] | [Generate implementation from spec] | [Level 2] |
| [e.g., `/pr`] | [Create pull request with summary] | [Level 2] |
| [Add your commands] | | |

---

# 🔧 Development Environment Setup

## Decision: Development Environment Type

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Context
[Where will source code live and development occur?]

### Considered Options
- **Cloud development environment** (EC2, Azure VM, etc.)
- **Local development** (laptop/desktop)
- **Hybrid** (local + cloud resources)
- **Container-based** (Codespaces, Gitpod)

### Decision
**Selected**: [Your choice]

### Rationale
- **Security considerations**: [Why this protects code/data]
- **Cost considerations**: [Budget impact]
- **Workflow preferences**: [Developer experience]
- **Compliance requirements**: [Regulatory needs]

### Configuration
- **Access Method**: [SSH / SSM / RDP / Browser / Local]
- **MFA Required**: [Yes / No]
- **Allowed Clients**: [Which devices can access]
- **Session Duration**: [How long sessions can be active]

---

## Decision: Source Code Location

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Decision
**Source code lives on**: [Development environment / Local machine / Both]

### Rationale
[Why this choice aligns with security and workflow requirements]

### Data Flow Rules
- **Code editing location**: [Where code can be edited and committed from]
- **Secrets management**: [Where secrets are stored and accessed]
- **Production data policy**: [Rules about production data access]
- **Local copies**: [What can/cannot be copied locally]

---

# 🌍 Environment Strategy

## Decision: Environment Definitions

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Environments

#### Development
- **Purpose**: [Primary development and testing]
- **Location**: [Cloud / On-prem / Hybrid - be specific]
- **Access Control**: [Who can access and how]
- **Data Policy**: [What data is allowed - test data only?]
- **Deployment**: [How code gets here - automatic? manual?]

#### Staging (if applicable)
- **Purpose**: [Pre-production validation]
- **Location**: [Cloud / On-prem]
- **Access Control**: [Who can access and how]
- **Data Policy**: [Production-like data? Anonymized?]
- **Deployment**: [How code gets here]

#### Production
- **Purpose**: [Live application serving users]
- **Location**: [Cloud / On-prem]
- **Access Control**: [Strict controls, MFA, etc.]
- **Data Policy**: [Real user data, compliance requirements]
- **Deployment**: [Manual gate? Automated with approval?]

### Promotion Workflow
[How do changes move from Dev → Staging → Prod?]

1. [Step 1 - e.g., "Developer merges to dev branch"]
2. [Step 2 - e.g., "CI runs tests"]
3. [Step 3 - e.g., "Auto-deploy to Dev"]
4. [Step 4 - e.g., "Create PR to main"]
5. [Step 5 - e.g., "Manual approval required"]
6. [Step 6 - e.g., "Deploy to Staging"]
7. [Step 7 - e.g., "Validation in Staging"]
8. [Step 8 - e.g., "Manual promotion to Prod"]

### CI/CD Automation
- **Automated**: [What deploys automatically]
- **Manual Gates**: [Where human approval required]
- **Approval Requirements**: [Who must approve promotions]

---

# 🔒 Security and Access Control

## Decision: Access Control Method

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Context
[How do developers and systems access environments?]

### Considered Options
- **SSH with key pairs**
- **SSM** (AWS Systems Manager Session Manager)
- **Bastion host**
- **VPN**
- **Azure Bastion**
- **Other**: [specify]

### Decision
**Selected**: [Your choice]

### Rationale
- **Security benefits**: [Why this is secure]
- **Compliance alignment**: [How this meets requirements]
- **Usability**: [Developer experience considerations]
- **Auditability**: [How access is logged]

### Configuration
- **MFA Required**: [Yes / No - specify when]
- **Session Logging**: [Yes / No - where logs go]
- **Session Duration Limits**: [e.g., 12 hours max]
- **Privileged Access**: [How admin access is granted]

---

## Decision: API Security Layer

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Context
[How are APIs protected from attacks?]

### Considered Options
- **Cloud-native WAF** (AWS WAF, Azure WAF, GCP Armor)
- **Third-party WAF** (Cloudflare, Akamai)
- **API Gateway with auth only**
- **Custom security layer**

### Decision
**Selected**: [Your choice]

### Security Controls
- **IP Filtering**: [Yes / No - explain allowlist policy]
- **Rate Limiting**: [Yes / No - explain limits per IP/key]
- **Request Validation**: [What validations are applied]
- **Enforcement Mode**: [Block malicious requests in prod, count in dev, etc.]
- **DDoS Protection**: [How DDoS is mitigated]

### Invariant
[State clearly if this is non-negotiable]
Example: "All public APIs MUST have WAF protection. AI agents MUST NOT bypass or disable WAF."

---

# 🏛️ Architectural Patterns

## Pattern: Cloud Service Factory

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Context
[How do you create cloud service clients consistently across your applications?]

### Implementation
- **Factory Location**: [e.g., `src/common/cloud_factory.py`]
- **Configuration Source**: [Environment variables / Config service / Parameter store]
- **Test Isolation Method**: [Mocks / LocalStack / Test doubles]

### Services Managed Through Factory
- [Service 1: e.g., Object Storage (S3/Blob Storage)]
- [Service 2: e.g., NoSQL Database (DynamoDB/CosmosDB)]
- [Service 3: e.g., Message Queue (SQS/Service Bus)]
- [Service 4: e.g., AI Models (Bedrock/Azure OpenAI)]

### Invariant
"All cloud service access MUST go through the factory. Direct SDK client creation is PROHIBITED."

### AI Agent Guardrails
- AI must always use factory functions
- AI must refactor any direct SDK usage
- AI must add new services to factory if needed

---

## Pattern: Multi-Tenant Data Isolation

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Context
[How do you prevent data leakage between tenants/clients/customers?]

### Considered Approaches
- **Database per tenant** - [Pros/cons for your case]
- **Schema per tenant** - [Pros/cons]
- **Row-level security** - [Pros/cons]
- **Partition key scoping** - [Pros/cons]

### Decision
**Selected**: [Your approach]

### Implementation Details
- **Tenant Identifier**: [How tenants are identified - customer ID, org ID, etc.]
- **Key/Index Pattern**: [e.g., `TENANT#{id}#{entity_type}#{entity_id}`]
- **Validation Layer**: [Where tenant ID is validated - API gateway, handler, service layer]
- **Cross-Tenant Queries**: [Allowed / Prohibited - explain]

### Invariant
"All data access MUST include tenant ID validation. Cross-tenant queries are PROHIBITED unless explicitly approved."

### AI Agent Guardrails
- AI must validate tenant ID at request boundary
- AI must include tenant ID in all data keys/queries
- AI must never allow cross-tenant access

---

## Pattern: Serverless Deployment Bundling

*(Skip this section if not using serverless)*

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Context
[If using serverless functions, how are they packaged and deployed?]

### Platform
**Selected**: [AWS Lambda / Azure Functions / Google Cloud Functions]

### Bundling Strategy
- **Directory Structure**: [Describe required structure]
- **Dependency Management**: [How dependencies are included]
- **Build Tool**: [CDK bundling / SAM / Serverless Framework / zip script]
- **Validation**: [How bundling correctness is verified]

### Invariant
"Lambda functions MUST follow standard bundling structure. Import failures due to incorrect bundling are unacceptable."

---

# 🧪 Testing Strategy

## Decision: Test Tier Definitions

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Fast Tests (Tier 1)
- **Definition**: [Unit tests, pure functions, no I/O]
- **Target Duration**: [e.g., < 1 second per test]
- **When Run**: [On every save / Before commit / In CI]
- **Framework**: [pytest / Jest / JUnit / etc.]
- **Coverage Target**: [e.g., > 80% of pure business logic]

### Medium Tests (Tier 2)
- **Definition**: [Integration tests with mocked external services]
- **Target Duration**: [e.g., < 10 seconds per test]
- **When Run**: [Before commit / In CI]
- **Framework**: [Same as fast or different]
- **Coverage Target**: [e.g., > 60% of integration points]

### Slow Tests (Tier 3)
- **Definition**: [E2E tests with real services]
- **Target Duration**: [e.g., < 5 minutes per suite]
- **When Run**: [Before deployment / Scheduled nightly]
- **Framework**: [Playwright / Selenium / Cypress / etc.]
- **Coverage Target**: [e.g., Critical user workflows]

### Test Organization
- **Fast tests location**: [e.g., `tests/unit/`]
- **Medium tests location**: [e.g., `tests/integration/`]
- **Slow tests location**: [e.g., `tests/e2e/`]
- **Markers/Tags**: [e.g., `@pytest.mark.fast`, `@pytest.mark.slow`]

---

# 📝 Workflows and Processes

## Decision: Version Control and Branching

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Branching Strategy
- **Main Branch**: [main / master / production]
- **Development Branch**: [dev / develop / None if trunk-based]
- **Feature Branches**: [Naming: `feature/*`, `feat/*`, other?]
- **Hotfix Branches**: [Naming: `hotfix/*`, `fix/*`]
- **Integration Branches**: [When used and naming]

### PR Workflow
- **Review Required**: [Yes / No - how many reviewers?]
- **AI as Reviewer**: [Can AI agents approve? Or human-only?]
- **CI Checks Required**: [What checks must pass before merge?]
- **Merge Strategy**: [Squash / Merge commit / Rebase]

### AI Agent Rules
- **Never commit to main directly**: [Yes - enforced by branch protection]
- **Branch naming for AI sessions**: [e.g., `ai/YYYY-MM-DD-description`]
- **Commit message format**: [Conventional commits / Other format]
- **Co-author attribution**: [Required in commit messages]

---

## Decision: TodoWrite Usage

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### When Required
AI agents must use TodoWrite for:
- [ ] Multi-step tasks (> 3 steps)
- [ ] Complex refactoring
- [ ] Slice implementation
- [ ] [Other criteria]

### Task Granularity
**Preference**: [Fine-grained (many small tasks) / Coarse-grained (fewer larger tasks)]

**Guidelines**: [What makes a good task size?]

### Tool Integration
**How TodoWrite is used**: [Built into AI agent / Manual tracking / GitHub Issues / Project management tool]

---

## Decision: Slice-Based Development

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Slice Definition
[How do you define a vertical slice in your context?]

Example: "A slice delivers end-to-end value from UI to database, is independently deployable, and can be completed in 1-3 days."

### Slice Size Guidelines
- **Target**: [e.g., 1-3 days of work]
- **Maximum**: [e.g., 1 week]
- **Minimum**: [e.g., Must include at least UI + API + data]

### Slice Documentation
- **Template Location**: [Where slice specs are stored]
- **Required Sections**: [What must be in each slice spec]
- **Approval Process**: [Who reviews/approves slice specs]

---

## Decision: Work Item Lifecycle

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Context
[How do you track work from request to completion? This documents your implementation of AOSD Principle 11.]

### Work Tracking Platform

**Considered Options**:
- **GitHub Issues** - [Native integration with repos, good for open source]
- **Jira** - [Enterprise features, complex workflows]
- **Linear** - [Modern UX, fast, developer-focused]
- **Azure DevOps** - [Microsoft ecosystem integration]
- **Shortcut** - [Balance of simplicity and features]
- **Other**: [specify]

**Selected**: [Your choice]

### Rationale
- [Why this platform suits your workflow]
- [Integration with existing tools]
- [Team familiarity]
- [Cost considerations]

### Work Item Types
Define the work item types you use:

| Type | Purpose | Example |
|------|---------|---------|
| [e.g., Feature] | [New functionality] | [Add user authentication] |
| [e.g., Bug] | [Defect fix] | [Login button not responding] |
| [e.g., Task] | [Technical work] | [Refactor database queries] |
| [e.g., Spike] | [Research/investigation] | [Evaluate caching options] |

### Work Item Template
Required fields for all work items:
- [ ] Title (clear, action-oriented)
- [ ] Description (context and requirements)
- [ ] Acceptance Criteria (when is it done?)
- [ ] Priority (how urgent?)
- [ ] Labels/Tags (categorization)
- [ ] [Add your required fields]

### Branch Naming Convention
How branches link to work items:

**Pattern**: `[type]/[id]-[description]`

**Examples**:
- `feature/123-add-user-auth`
- `fix/456-login-button`
- `task/789-refactor-queries`

### Commit Message Format
How commits reference work items:

**Pattern**: `[type]: [description] (#[id])`

**Examples**:
- `feat: add login endpoint (#123)`
- `fix: resolve null pointer in auth (#456)`
- `refactor: optimize database queries (#789)`

### Lifecycle Stages & Automation
Map AOSD lifecycle stages to your platform:

| AOSD Stage | Your Status | Automation |
|------------|-------------|------------|
| Creation | [e.g., Open] | [Auto-created from template] |
| Analysis | [e.g., Ready for Dev] | [Manual move after review] |
| Branch Created | [e.g., In Progress] | [Auto on branch push] |
| Implementation | [e.g., In Progress] | [Updated by commits] |
| Review | [e.g., In Review] | [Auto on PR creation] |
| Merge & Closure | [e.g., Done] | [Auto on PR merge] |

### Closure Criteria
A work item can only be closed when:
- [ ] All acceptance criteria verified
- [ ] Tests passing
- [ ] Code merged to main
- [ ] Documentation updated (if applicable)
- [ ] [Add your criteria]

### AI Agent Guidelines
For AI agents working with work items:
- Always reference work item ID in commits
- Update work item status when starting work
- Add comments for significant decisions
- Link related work items when discovered
- Never close work items without verification

---

## GitHub Workflow (Example Implementation)

*This section provides a complete example for teams using GitHub Issues. Adapt to your platform.*

### Work Item Creation (GitHub Issues)

**Creating an Issue**:
```markdown
Title: [Clear, action-oriented description]

## Description
[Context and background for the work]

## Acceptance Criteria
- [ ] [Criterion 1]
- [ ] [Criterion 2]
- [ ] [Criterion 3]

## Technical Notes
[Any implementation guidance or constraints]

## Related
- Depends on: #[number] (if applicable)
- Slice spec: [link to spec] (if applicable)
```

**Labels to Use**:
| Label | Purpose |
|-------|---------|
| `enhancement` | New feature or improvement |
| `bug` | Something isn't working |
| `documentation` | Documentation updates |
| `refactor` | Code improvement without behavior change |
| `security` | Security-related work |
| `priority:high` | Urgent work |
| `priority:low` | Can wait |

**AI Agent Tip**: When creating issues, always include acceptance criteria. This gives AI agents clear completion targets.

### Branch Creation from Issue

**Option 1: Command Line**
```bash
# From main branch
git checkout main
git pull origin main
git checkout -b feature/42-add-user-authentication
```

**Option 2: GitHub UI**
1. Open the issue
2. Click "Create a branch" in the right sidebar
3. Select branch name (follows your naming convention)
4. Choose to checkout locally or work in Codespaces

**Naming Convention**:
```
{type}/{issue-number}-{short-description}

Examples:
- feature/42-add-user-authentication
- fix/57-login-button-not-responding
- refactor/89-optimize-database-queries
- docs/103-update-api-documentation
```

### Implementation Cycle

**Commit Message Format**:
```
{type}: {description} (#{issue-number})

{optional body with more details}

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>
```

**Examples**:
```bash
git commit -m "feat: add login endpoint (#42)"
git commit -m "fix: resolve null pointer in auth handler (#57)"
git commit -m "refactor: extract user validation to separate module (#89)"
```

**Magic Keywords** (auto-close issues when PR merges):
- `Fixes #42` - Closes issue when PR merges
- `Closes #42` - Same as Fixes
- `Resolves #42` - Same as Fixes
- `Relates to #42` - Links without closing

**Push Regularly**:
```bash
git push origin feature/42-add-user-authentication
```

### Pull Request Process

**Creating a PR**:
```bash
gh pr create --title "Add user authentication (#42)" --body "$(cat <<'EOF'
## Summary
Brief description of what this PR does.

## Changes
- Change 1
- Change 2
- Change 3

## Test Plan
- [ ] Unit tests added
- [ ] Integration tests pass
- [ ] Manual testing completed

## Screenshots (if applicable)
[Add screenshots for UI changes]

Fixes #42

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

**PR Checklist**:
- [ ] Title references issue number
- [ ] Description explains changes clearly
- [ ] Tests added/updated
- [ ] CI checks passing
- [ ] Ready for review

**Review Process**:
1. **Automated Checks**: CI runs tests, linting, security scans
2. **Human Review**: Reviewer examines code changes
3. **AI Review** (optional): AI agent reviews for patterns/issues
4. **Address Feedback**: Make requested changes
5. **Approval**: Reviewer approves PR

### Merge and Closure

**Merge Strategy** (choose one for your team):
| Strategy | When to Use |
|----------|-------------|
| **Squash and merge** | Clean history, single commit per feature |
| **Merge commit** | Preserve full commit history |
| **Rebase and merge** | Linear history without merge commits |

**After Merge**:
1. Issue auto-closes (if PR used `Fixes #X`)
2. Branch deleted (configure in repo settings or manually)
3. Verify deployment (if CI/CD triggered)

**If Issue Doesn't Auto-Close**:
```bash
gh issue close 42 --comment "Implemented in PR #123"
```

### CI/CD Integration with GitHub Actions

**Example Workflow** (`.github/workflows/ci.yml`):
```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run Fast Tests
        run: npm test -- --tier=fast

      - name: Run Medium Tests
        run: npm test -- --tier=medium

  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Lint
        run: npm run lint

  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Security Scan
        run: npm audit
```

**Branch Protection Rules**:
Configure in Settings → Branches → Add rule for `main`:
- [x] Require pull request reviews before merging
- [x] Require status checks to pass before merging
- [x] Require branches to be up to date before merging
- [x] Include administrators

### AI Agent Workflow with GitHub

**For AI agents working with GitHub**:

1. **Starting Work on an Issue**:
   ```bash
   # View the issue
   gh issue view 42

   # Create branch
   git checkout -b feature/42-description

   # Start work...
   ```

2. **During Implementation**:
   ```bash
   # Commit with issue reference
   git commit -m "feat: implement user login (#42)"

   # Push changes
   git push origin feature/42-description
   ```

3. **Creating PR**:
   ```bash
   # Create PR that will close issue
   gh pr create --title "Add user login (#42)" --body "Fixes #42"
   ```

4. **After Review Feedback**:
   ```bash
   # Make changes, commit, push
   git commit -m "fix: address review feedback (#42)"
   git push
   ```

5. **Merging** (if authorized):
   ```bash
   gh pr merge 123 --squash --delete-branch
   ```

**AI Agent Rules for GitHub**:
- Always check issue exists before starting: `gh issue view {number}`
- Never push directly to main
- Always create PR for changes
- Include issue number in all commits
- Use `Fixes #X` in PR to auto-close issues
- Delete branch after merge

---

## Alternative Platform Placeholders

### Jira Integration
*Document your Jira workflow here if using Jira:*
- Project key and issue types
- Workflow states mapping
- Branch naming with Jira keys (e.g., `PROJ-123-description`)
- Smart commits for Jira
- Automation rules

### Linear Integration
*Document your Linear workflow here if using Linear:*
- Team and project structure
- Issue identifiers (e.g., `ENG-123`)
- Git branch integration
- Automation triggers
- Cycle management

### Azure DevOps Integration
*Document your Azure DevOps workflow here if using ADO:*
- Work item types
- Board configuration
- Branch policies
- Pipeline integration
- PR policies

---

# 💰 Cost Management

## Decision: AI Model Selection Heuristics

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Model Tiers

**Tier 1 (Fast/Cheap)**:
- **Models**: [e.g., Claude Haiku, GPT-3.5]
- **Use Cases**: [Simple tasks, high volume, code completion]
- **Cost**: [Approximate cost per 1M tokens]

**Tier 2 (Balanced)**:
- **Models**: [e.g., Claude Sonnet, GPT-4]
- **Use Cases**: [Standard development tasks, code review]
- **Cost**: [Approximate cost]

**Tier 3 (Powerful/Expensive)**:
- **Models**: [e.g., Claude Opus, GPT-4 Turbo]
- **Use Cases**: [Complex architecture, critical decisions, debugging hard problems]
- **Cost**: [Approximate cost]

### Selection Criteria
[How do you decide which tier to use for a given task?]

### Budget Thresholds
- **Monthly AI budget**: [$X]
- **Alert threshold**: [At $Y, review usage]
- **Hard limit**: [At $Z, require approval]

---

## Decision: Cost Monitoring

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Infrastructure Costs
- **Tracking Method**: [Cloud cost explorer / Third-party tool / Manual]
- **Review Frequency**: [Daily / Weekly / Monthly]
- **Owner**: [Who reviews and optimizes costs]

### AI/Model Costs
- **Tracking Method**: [API usage dashboard / Billing reports]
- **Review Frequency**: [Weekly / Monthly]
- **Owner**: [Who monitors AI costs]

---

# 🎓 Team and Training

## Decision: AOSD Adoption Approach

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Team Structure
- **Team Size**: [Solo / 2-5 / 6-10 / 10+]
- **AOSD Orchestrator(s)**: [Who fills this role]
- **Developers**: [How many, experience level]

### Training Approach
- [ ] Self-study with AOSD Framework
- [ ] NotebookLM with SYLLABUS
- [ ] Formal training sessions
- [ ] Pair programming with experienced orchestrator
- [ ] [Other approach]

### Onboarding Process
**For new team members**:
1. [Step 1 - e.g., Read AOSD Framework]
2. [Step 2 - e.g., Review this Orchestration Profile]
3. [Step 3 - e.g., Pair on first slice]
4. [Step 4 - e.g., Complete training exercise]

**Expected time to productivity**: [e.g., 1-2 weeks]

---

## Decision: Team Scaling Strategy

*See AOSD Framework "Scaling AOSD for Teams and Organizations" section for detailed guidance.*

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Current Scale
- [ ] **Solo Developer**: Single orchestrator, 1-3 AI agents
- [ ] **Small Team (2-5)**: Lightweight coordination, shared context
- [ ] **Medium Team (6-15)**: Formal processes, sub-teams
- [ ] **Large Enterprise (15+)**: Governance, dedicated platform team

### Slice Distribution Strategy
**Selected**: [Individual ownership / Pair ownership / Rotating ownership / Domain-based / Capacity-based]

**Rationale**: [Why this approach suits your team]

### Coordination Mechanisms
- [ ] Daily sync/standup
- [ ] Shared prompt library
- [ ] Integration branches for related features
- [ ] Slice ownership matrix
- [ ] Cross-team architecture reviews
- [ ] [Other mechanisms]

### Knowledge Sharing Practices
- [ ] Code reviews spread understanding
- [ ] Documentation required for AI-generated code
- [ ] Pair sessions on complex slices
- [ ] AI interaction recordings/summaries
- [ ] Rotating slice ownership
- [ ] [Other practices]

### Branch Ownership Model

| Branch Type | Owner | Review Required |
|-------------|-------|-----------------|
| Feature branches | [Individual / Pair] | [Yes/No] |
| Integration branches | [Team lead / Designated] | [Yes/No] |
| Release branches | [Release manager] | [Yes/No] |

---

# 📊 Success Metrics

## Metrics You Track

### Development Velocity
- [ ] Slices completed per sprint
- [ ] Time from spec to production
- [ ] AI agent utilization rate
- [ ] Developer productivity (features per dev-month)

### Quality
- [ ] Test coverage percentage
- [ ] Defect escape rate
- [ ] Mean time to resolution (MTTR)
- [ ] Production incidents per release

### Cost
- [ ] Infrastructure costs (monthly)
- [ ] AI API costs (monthly)
- [ ] Cost per feature
- [ ] ROI on AI tooling

### Security
- [ ] Security incidents
- [ ] Vulnerability remediation time (SLA)
- [ ] Compliance violations
- [ ] Failed security scans

### AI-Specific
- [ ] AI agent success rate (tasks completed correctly)
- [ ] Human intervention rate
- [ ] Code generated vs code reviewed/edited
- [ ] Time saved by AI assistance

---

# 🔄 Profile Evolution and Governance

## Decision Review Process

**Review Frequency**: [How often do you review these decisions?]
- **Quarterly** reviews for major decisions
- **As-needed** for tactical changes
- **Annually** for strategic reassessment

**Triggers for Review**:
- New cloud platform features become available
- Cost changes significantly
- Security incidents occur
- Team grows/changes
- New compliance requirements
- Better AI models released

## Documentation Updates

**Owner**: [Who maintains this Orchestration Profile?]

**Review Cycle**: [Monthly / Quarterly / As-needed]

**Change Process**:
1. [How are updates proposed]
2. [Who reviews and approves]
3. [How changes are communicated to team]
4. [How applications are updated]

## Cross-Artifact Sync Process

*Per META_GUIDE.md: No update is complete unless all dependent artifacts are updated.*

**When Profile Changes**:

Changes to this Orchestration Profile may require updates to:

- [ ] Level 3 application AOSD/ folders (if patterns change)
- [ ] Team prompt libraries (if agent configuration changes)
- [ ] Onboarding documentation (if processes change)
- [ ] CI/CD pipelines (if deployment patterns change)

**Sync Checklist** (use when making profile changes):
- [ ] Identified all dependent artifacts
- [ ] Updated all affected Level 3 docs
- [ ] Communicated changes to team
- [ ] Verified consistency across all artifacts
- [ ] Updated changelog with change summary

---

# 🔐 Restrictive Environment Considerations

If your organization operates in export-controlled, network-isolated, or heavily regulated environments, document your adaptations here.

*See AOSD Framework "AOSD in Restrictive Environments" section for detailed guidance.*

## Decision: Environment Classification

**Status**: [Proposed / Accepted / Not Applicable]

**Decision Date**: [YYYY-MM-DD]

### Environment Type
- [ ] **Standard**: Full access to commercial AI and cloud services
- [ ] **Government**: Network-isolated, cleared personnel required
- [ ] **ITAR/Export-Controlled**: Restricted data handling, citizenship requirements
- [ ] **Proprietary Corporate**: Trade secret protection, internal-only tools
- [ ] **Regulated Healthcare**: HIPAA, FDA validation requirements
- [ ] **Hybrid**: Combination of above

### AI Capability Assessment

| Capability | Available | Alternative if Not |
|------------|-----------|-------------------|
| Commercial AI APIs (Claude, GPT) | [Yes/No] | [Alternative] |
| Self-hosted AI models | [Yes/No] | [Alternative] |
| IDE AI assistants | [Yes/No] | [Alternative] |
| AI code review | [Yes/No] | [Alternative] |
| External documentation access | [Yes/No] | [Alternative] |

### Adaptation Strategy

**Planning vs Execution Split**:
- Planning done: [In restricted env / External with sanitization / Both]
- Execution done: [In restricted env only / Dual environment]

**Prompt Constraints Applied**:
- [ ] No multi-file changes
- [ ] Explicit scope boundaries
- [ ] Manual review required
- [ ] Audit logging of AI usage
- [ ] [Other constraints]

**Compliance Documentation**:
- AI usage tracking: [Method]
- Change attribution: [How AI vs human distinguished]
- Audit trail: [Where maintained]

### Dual-Environment Workflow (if applicable)

If you maintain both restricted and unrestricted development:

**Unrestricted Environment** (if any):
- Used for: [What work happens here]
- Transfer method: [How work moves to restricted env]
- Sanitization process: [How sensitive info is removed]

**Restricted Environment**:
- Used for: [What work happens here]
- AI capabilities: [What's available]
- Compliance requirements: [Specific regulations]

---

# 📎 Pattern Library

## Your Patterns Collection

This section links to detailed pattern documentation for your organization.

### Infrastructure Patterns
- [Pattern 1](link) - [One-line description]
- [Pattern 2](link) - [One-line description]

### Data Patterns
- [Pattern 1](link) - [One-line description]
- [Pattern 2](link) - [One-line description]

### Security Patterns
- [Pattern 1](link) - [One-line description]
- [Pattern 2](link) - [One-line description]

### AI Integration Patterns
- [Pattern 1](link) - [One-line description]
- [Pattern 2](link) - [One-line description]

---

# 🧠 AI Integration Into Applications

AOSD Goal 5 calls for AI to be integrated as first-class runtime capabilities. This section documents how your organization embeds AI into applications beyond development-time assistance.

*See AOSD Framework "Integrating AI Into Applications" section for detailed guidance and patterns.*

## Decision: AI Integration Level

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Context
[What level of AI integration do your applications target? How deeply is AI embedded?]

### Integration Levels Used

| Level | Used | Applications | Notes |
|-------|------|--------------|-------|
| **Surface** (chatbots, FAQ) | [Yes/No] | [Which apps] | [Notes] |
| **Functional** (specific tasks) | [Yes/No] | [Which apps] | [Notes] |
| **Contextual** (context-aware help) | [Yes/No] | [Which apps] | [Notes] |
| **Embedded** (core workflows) | [Yes/No] | [Which apps] | [Notes] |
| **Intelligent** (adaptive behavior) | [Yes/No] | [Which apps] | [Notes] |

### AI Service Architecture

**Pattern Used**: [AI as Service Layer / Embedded / Other]

**Configuration**:
- **AI Service Location**: [Internal / External API / Hybrid]
- **Fallback Strategy**: [Graceful degradation approach]
- **Timeout Policy**: [AI request timeout settings]
- **Response Validation**: [How AI outputs are validated]

---

## Decision: Contextual Training Approach

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Context
[How do your applications provide in-context training and help to users?]

### Training Patterns Used

- [ ] **Inline Explanations**: AI-generated contextual help
- [ ] **Guided Workflows**: Step-by-step AI assistance
- [ ] **Proactive Error Prevention**: AI warns before mistakes
- [ ] **Adaptive Help System**: Context-aware dynamic help
- [ ] **None**: Traditional static help only

### Implementation Details

**Explanation Trigger**: [Hover time / Click / Confusion detection / Manual request]

**Experience Level Detection**: [User profile / Behavioral analysis / Self-declared / None]

**Help Content Source**: [AI-generated / Hybrid / Static with AI search]

### Measuring Effectiveness

**Metrics Tracked**:
- [ ] Time to first success
- [ ] Help invocations per task
- [ ] Error rates
- [ ] Feature adoption
- [ ] [Other metrics]

---

## Decision: Proactive Suggestion Strategy

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Context
[How do your applications provide AI suggestions without being intrusive (avoiding "Clippy syndrome")?]

### Suggestion Approach

**Philosophy**: [Conservative (few suggestions) / Moderate / Aggressive (many suggestions)]

**Three Gates Implementation**:
1. **Relevance Gate**: [How relevance is determined]
2. **Value Gate**: [Minimum value threshold for suggestions]
3. **Timing Gate**: [When suggestions are appropriate]

### Suggestion Controls

**User Controls Provided**:
- [ ] Global on/off for suggestions
- [ ] Category-level controls
- [ ] "Don't show again" per type
- [ ] Quiet hours/focus mode
- [ ] [Other controls]

### Anti-Patterns to Avoid

Ensure your implementations avoid:
- [ ] Interrupting users mid-task
- [ ] Stating the obvious
- [ ] Repeating dismissed suggestions
- [ ] Blocking modal dialogs
- [ ] Random timing

---

## Decision: AI Security Monitoring

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Context
[How do your applications use AI for runtime security monitoring?]

### Security Monitoring Types

| Type | Implemented | Configuration |
|------|-------------|---------------|
| **Behavioral Anomaly Detection** | [Yes/No] | [Details] |
| **Content-Aware Data Protection** | [Yes/No] | [Details] |
| **Real-Time Input Validation** | [Yes/No] | [Details] |
| **Prompt Injection Detection** | [Yes/No] | [Details] |

### Response Actions

**Available Responses**:
- [ ] Alert (security team notification)
- [ ] Log (audit trail only)
- [ ] Warn (user notification)
- [ ] Block (prevent action)

**Thresholds**: [How response levels are determined]

### Privacy Considerations

- **Data Collected**: [What security data is collected]
- **Retention Period**: [How long security logs are kept]
- **User Transparency**: [How users are informed of monitoring]
- **Anonymization**: [Where anonymization is applied]

---

## Decision: Personalization Strategy

**Status**: [Proposed / Accepted / Deprecated]

**Decision Date**: [YYYY-MM-DD]

### Context
[How do your applications personalize the experience based on user behavior?]

### Personalization Types Used

| Type | Implemented | Scope |
|------|-------------|-------|
| **Preference-Based** (explicit choices) | [Yes/No] | [Scope] |
| **Behavioral** (usage patterns) | [Yes/No] | [Scope] |
| **Contextual** (current situation) | [Yes/No] | [Scope] |
| **Predictive** (anticipating needs) | [Yes/No] | [Scope] |
| **Adaptive** (interface evolution) | [Yes/No] | [Scope] |

### Implementation Details

**Data Storage**:
- **User Preferences**: [Where stored, how synced]
- **Behavioral Data**: [Where stored, retention period]
- **AI Insights**: [Where stored, aggregation level]

**Privacy Controls Provided**:
- [ ] View personalization data
- [ ] Reset personalization
- [ ] Export data
- [ ] Category opt-out
- [ ] Full disable

### Avoiding Pitfalls

Measures to prevent:
- [ ] Filter bubbles (periodic new feature surfacing)
- [ ] Overfitting (rolling windows, pattern repetition requirements)
- [ ] Creepy factor (transparency, limited sensitive area personalization)
- [ ] Stale patterns (time decay)
- [ ] Cross-device issues (sync strategy)

---

## AI Integration Invariants

**Non-Negotiable Rules for AI in Applications**:

1. [e.g., "AI suggestions must pass Three Gates before display"]
2. [e.g., "AI failures must not break core application functionality"]
3. [e.g., "All AI security alerts must be logged with correlation IDs"]
4. [e.g., "Users must have control to disable personalization"]
5. [Add your invariants]

---

## AI Integration Quick Reference

### For Teams Implementing AI Features

| Question | Answer |
|----------|--------|
| What AI integration levels do we use? | [Levels] |
| How do we avoid Clippy behavior? | [Three Gates reference] |
| What security monitoring is active? | [Types] |
| How is personalization data handled? | [Approach] |
| What user controls are required? | [List] |

### For AI Agents Building AI-Integrated Features

- Follow [AI Integration Level] patterns
- Implement Three Gates for any proactive suggestions
- Ensure graceful degradation when AI services unavailable
- Include user controls for AI-powered features
- Log all AI interactions with correlation IDs

---

# 🎯 Quick Reference for AI Agents

## Key Invariants (Non-Negotiable)
1. [Invariant 1 - e.g., "All APIs must have WAF protection"]
2. [Invariant 2 - e.g., "Use cloud factory, never direct SDK calls"]
3. [Invariant 3 - e.g., "Validate tenant ID on every request"]
4. [Invariant 4 - e.g., "Never commit directly to main branch"]
5. [Add your critical invariants]

## Key Patterns
1. [Pattern 1 - e.g., "Environment Factory"]
2. [Pattern 2 - e.g., "Multi-Tenant Data Isolation"]
3. [Pattern 3 - e.g., "FCIS Architecture"]
4. [Add your key patterns]

## Common Workflows
- **New feature**: [Slice spec → TodoWrite → Implement → Test → PR → Review → Merge]
- **Bug fix**: [Reproduce → Fix → Test → PR → Deploy]
- **Architecture change**: [ADR → ChatGPT review → Implement → Validate]

---

# 🔗 References

## Related Documentation
- **AOSD Framework**: [Link to Level 1 framework repo]
- **Application Docs**: [Links to Level 3 docs in application repos]
- **Team Wiki**: [Internal documentation]

## External Resources
- **Cloud Platform Docs**: [Link to AWS/Azure/GCP docs]
- **AI Agent Docs**: [Links to Claude/ChatGPT documentation]
- **Security Standards**: [Links to compliance frameworks]

---

# 🏁 Conclusion

This Orchestration Profile documents your specific implementation of AOSD methodology. It should evolve as you learn and as technology changes.

**Remember**:
- This is Level 2 - your specific choices
- Reference Level 1 (AOSD Framework) for principles
- Each application (Level 3) builds on this profile
- Keep this updated as patterns evolve

**Next Steps**:
1. Share this profile with your team
2. Reference from application AOSD/ folders
3. Update as you discover better patterns
4. Create your first slice using this profile

---

**End of Orchestration Profile Template**
