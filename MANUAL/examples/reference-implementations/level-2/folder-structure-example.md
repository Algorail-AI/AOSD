# Example Orchestration Profile Folder Structure

**Reference: How to organize a Level 2 repository**

---

## Overview

This example shows a complete folder structure for an Orchestration Profile repository. The structure supports AI agent consumption, team onboarding, and maintainability.

---

## Example Structure

```
acme-aosd/                              # Level 2 Orchestration Profile
├── README.md                           # Profile overview and quick start
├── ORCHESTRATION_PROFILE.md            # Main profile (from IMPLEMENTATION_TEMPLATE)
├── CHANGELOG.md                        # Profile version history
│
├── agents/                             # AI agent configurations
│   ├── README.md                       # Agent overview
│   ├── builder-config.md               # Claude Code configuration
│   ├── reviewer-prompts.md             # ChatGPT review prompts
│   └── shared-context.md               # Context shared across agents
│
├── invariants/                         # Non-negotiable rules
│   ├── README.md                       # Invariants overview
│   ├── security-invariants.md          # Security rules
│   ├── architecture-invariants.md      # Architecture rules
│   └── process-invariants.md           # Process rules
│
├── workflows/                          # CI/CD and automation
│   ├── README.md                       # Workflow overview
│   ├── github-actions/                 # GitHub Actions workflows
│   │   ├── ci.yml                      # Continuous integration
│   │   ├── deploy-dev.yml              # Dev deployment
│   │   ├── deploy-staging.yml          # Staging deployment
│   │   └── deploy-prod.yml             # Production deployment
│   └── scripts/                        # Helper scripts
│       ├── validate-invariants.sh      # Invariant checker
│       └── generate-evidence.sh        # Evidence package generator
│
├── patterns/                           # Organization-specific patterns
│   ├── README.md                       # Patterns overview
│   ├── aws-service-factory.md          # AWS client factory pattern
│   ├── multi-tenant-isolation.md       # Tenant data isolation
│   └── error-handling.md               # Standard error handling
│
├── templates/                          # Organization-specific templates
│   ├── slice-spec.md                   # Customized slice template
│   ├── adr.md                          # Customized ADR template
│   └── pr-description.md               # PR description template
│
├── training/                           # Team onboarding
│   ├── README.md                       # Training overview
│   ├── quick-start.md                  # 15-minute quick start
│   ├── developer-guide.md              # Detailed developer guide
│   └── exercises/                      # Practice exercises
│       ├── first-slice.md
│       └── first-review.md
│
└── decisions/                          # Profile-level ADRs
    ├── 001-cloud-platform.md           # Why AWS
    ├── 002-ai-agents.md                # Why Claude + ChatGPT
    └── 003-branching-strategy.md       # Why feature branches
```

---

## Key Directories Explained

### `agents/`

**Purpose**: AI agent configurations and prompts.

**Contents**:
- Agent-specific setup instructions
- Custom prompts for each agent role
- Shared context all agents should know

**Example Entry** (`builder-config.md`):
```markdown
# Claude Code Builder Configuration

## Role
Primary Builder/Implementer agent for day-to-day development.

## Setup
- Model: claude-sonnet-4 (default)
- Escalate to claude-opus-4 for: architecture decisions, security reviews

## Custom Commands
- `/slice {spec}` - Implement from slice spec
- `/review` - Self-review before PR

## Context to Include
Always read before starting work:
- `ORCHESTRATION_PROFILE.md` (invariants section)
- `invariants/security-invariants.md`
- Application's `AOSD/INVARIANTS.md`
```

### `invariants/`

**Purpose**: Non-negotiable rules that AI agents must follow.

**Contents**:
- Security invariants (authentication, authorization, data protection)
- Architecture invariants (patterns, boundaries, dependencies)
- Process invariants (review requirements, deployment gates)

**Why Separate Files**: Makes it easy to reference specific invariant sets. AI agents can load only relevant invariants for a task.

### `workflows/`

**Purpose**: CI/CD automation aligned with AOSD practices.

**Contents**:
- GitHub Actions (or equivalent) workflow definitions
- Helper scripts for validation and evidence generation
- Deployment automation

### `patterns/`

**Purpose**: Organization-specific implementation patterns.

**Contents**:
- Patterns tailored to your platform choices
- Concrete implementations of AOSD principles
- Code examples and templates

### `training/`

**Purpose**: Team onboarding and skill development.

**Contents**:
- Quick start guide (< 15 minutes to first contribution)
- Detailed developer guide
- Practice exercises

### `decisions/`

**Purpose**: Architecture Decision Records for profile-level choices.

**Contents**:
- Major decisions with context and rationale
- Follows ADR template format
- Referenced from main profile

---

## Minimal Structure

For solo developers or small teams, start with:

```
my-aosd/
├── README.md
├── ORCHESTRATION_PROFILE.md
├── invariants/
│   └── core-invariants.md
└── workflows/
    └── github-actions/
        └── ci.yml
```

Expand as needs grow.

---

## File Naming Conventions

| Type | Pattern | Example |
|------|---------|---------|
| Main docs | UPPERCASE.md | `ORCHESTRATION_PROFILE.md` |
| Reference docs | lowercase-kebab.md | `security-invariants.md` |
| Workflows | lowercase-kebab.yml | `deploy-staging.yml` |
| ADRs | NNN-title.md | `001-cloud-platform.md` |

---

## Cross-Referencing

Your Level 2 profile should reference:

**Upward (to Level 1)**:
```markdown
See [AOSD Framework](https://github.com/your-org/AOSD) for core methodology.
```

**Downward (from Level 3 apps)**:
```markdown
# In application's AOSD/README.md
Orchestration Profile: [acme-aosd](https://github.com/acme/acme-aosd)
```

---

## Maintenance

### When to Update

- New team member joins (check training materials)
- Platform or tool changes (update agents, workflows)
- New invariant discovered (add to invariants/)
- Pattern emerges (add to patterns/)

### Sync Checklist

When updating the profile:
- [ ] All applications notified of changes
- [ ] Training materials updated
- [ ] Agent configurations still valid
- [ ] Workflows still function
- [ ] CHANGELOG updated

---

**End of Example**
