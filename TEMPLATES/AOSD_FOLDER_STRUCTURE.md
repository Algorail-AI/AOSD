# AOSD/ Folder Structure for Applications
**Level 3: Application-Specific AOSD Documentation**
**Copyright Algorail AI, LLC 2025**

---

# Purpose

This document defines the standard structure for the `AOSD/` folder in application repositories that use AOSD methodology.

---

# Standard Structure

```
your-application/
├── src/
├── tests/
├── AOSD/                          # All AOSD documentation lives here
│   ├── README.md                   # Overview and quick links
│   ├── AOSD.md                     # Main Level 3 documentation (from template)
│   ├── ORCHESTRATION_PROFILE.md    # Link to Level 2 + any overrides
│   ├── decisions/                  # Application-specific ADRs
│   │   ├── 001-database-choice.md
│   │   ├── 002-authentication-strategy.md
│   │   └── 003-ai-feature-approval.md
│   ├── slices/                     # Slice specifications
│   │   ├── 2025-01-15-user-authentication.md
│   │   ├── 2025-01-20-dashboard-ui.md
│   │   └── 2025-02-01-reporting-feature.md
│   ├── patterns/                   # App-specific patterns (optional)
│   │   ├── custom-validation-pattern.md
│   │   └── data-migration-pattern.md
│   └── context/                    # Context nuggets for AI agents
│       ├── README.md               # Index of available context nuggets
│       ├── auth-flow.md            # Authentication flow context
│       ├── data-model.md           # Data model context
│       └── api-patterns.md         # API design patterns context
├── .ai/                            # AI agent context memory (optional)
│   ├── memory/
│   │   ├── session/                # Current session context (ephemeral)
│   │   ├── decisions/              # Key decisions made during development
│   │   ├── learnings/              # What AI learned about codebase
│   │   └── handoffs/               # Context for agent transitions
│   ├── context/
│   │   └── project-summary.md      # High-level project understanding
│   └── .gitignore                  # Exclude sensitive/ephemeral files
├── README.md
└── [other app files]
```

---

# File Descriptions

## `AOSD/README.md`

Quick orientation file with links to key resources.

**Template**:
```markdown
# AOSD Documentation

This application uses AOSD (AI Orchestrated Systems Development) methodology.

## Quick Links
- [Main AOSD Documentation](./AOSD.md) - Application-specific invariants and patterns
- [Orchestration Profile](./ORCHESTRATION_PROFILE.md) - Link to Level 2 profile
- [Architecture Decisions](./decisions/) - ADRs for this application
- [Slice Specifications](./slices/) - Work breakdown and planning

## For AI Agents
When working on this codebase:
1. Read `AOSD.md` for application-specific context
2. Follow invariants and patterns documented here
3. Reference Orchestration Profile for organization-wide patterns
4. Check recent slices for context on current work

## For Developers
- New to AOSD? Start with: [AOSD Framework](https://github.com/Algorail-AI/AOSD)
- New to this app? Read `AOSD.md` for architecture overview
- Creating a slice? Use templates in Orchestration Profile
```

---

## `AOSD/AOSD.md`

Main Level 3 documentation created from `APP_TEMPLATE.md`.

Contains:
- Application overview and architecture
- Application-specific invariants
- Application-specific patterns
- Data model
- Testing approach
- Deployment process
- AI agent context

**Source**: Copy from `TEMPLATES/APP_TEMPLATE.md` in AOSD Framework repo

---

## `AOSD/ORCHESTRATION_PROFILE.md`

Links to your Level 2 Orchestration Profile and documents any app-specific overrides.

**Template**:
```markdown
# Orchestration Profile

This application uses: **[Your Orchestration Profile Name]**

## Profile Repository
[Link to your Level 2 repo, e.g., https://github.com/your-org/your-orchestration-profile]

## Profile Version
**[Version or commit hash for reproducibility]**

## Application-Specific Overrides

This application follows the Orchestration Profile with the following exceptions:

### Override 1: [Name]
- **Profile Default**: [What the profile specifies]
- **This App**: [What this app does differently]
- **Rationale**: [Why this app needs different approach]

### Override 2: [Name]
- **Profile Default**: [...]
- **This App**: [...]
- **Rationale**: [...]

## AI Agent Instructions

When working on this application:
1. Follow Orchestration Profile patterns by default
2. Apply overrides documented above
3. Consult `AOSD.md` for application-specific details
```

---

## `AOSD/decisions/`

Application-specific Architecture Decision Records (ADRs).

**Naming Convention**: `NNN-short-title.md` (e.g., `001-database-choice.md`)

**Template** (from Michael Nygard's ADR format):
```markdown
# ADR-NNN: [Title]

**Status**: [Proposed / Accepted / Deprecated / Superseded by ADR-XXX]

**Date**: YYYY-MM-DD

**Decision Maker(s)**: [Who made this decision]

## Context
[What is the issue we're trying to address? What factors are in play?]

## Decision
[What is the change we're proposing and/or doing?]

## Consequences

### Positive
- [Benefit 1]
- [Benefit 2]

### Negative
- [Tradeoff 1]
- [Tradeoff 2]

### Neutral
- [Other implication 1]

## Alternatives Considered
- **Alternative 1**: [Why not chosen]
- **Alternative 2**: [Why not chosen]

## Notes
[Any additional context, references, or implementation notes]
```

---

## `AOSD/slices/`

Slice specifications for work planning.

**Naming Convention**: `YYYY-MM-DD-short-title.md` (e.g., `2025-01-15-user-authentication.md`)

**Template**: Use `SLICE_SPEC_TEMPLATE.md` from your Orchestration Profile

**Purpose**:
- Plan vertical slices of work
- Provide context to AI agents
- Document implementation approach
- Track acceptance criteria

---

## `AOSD/patterns/` (Optional)

Application-specific patterns that don't exist in your Orchestration Profile.

**When to Create**:
- Pattern is unique to this application
- Pattern is reused multiple times in this codebase
- Pattern is complex enough to warrant documentation

**Template**:
```markdown
# [Pattern Name]

## Problem
[What problem does this solve in this application?]

## Context
[When should this pattern be used?]

## Solution
[How does this pattern solve the problem?]

## Implementation
[Specific code examples, file locations, etc.]

## Consequences
- **Benefits**: [...]
- **Tradeoffs**: [...]

## Related Patterns
- [Link to Orchestration Profile pattern if extending one]
- [Link to other app patterns if related]
```

---

## `.ai/` (Optional)

Agent Context Memory Files for persistent AI context across sessions.

**Purpose**:
- Maintain AI agent context between sessions
- Enable smooth handoffs between different agents
- Reduce human effort in re-explaining project context
- Support multi-agent coordination

**Folder Structure**:
```
.ai/
├── memory/
│   ├── session/           # Current session context (ephemeral, gitignored)
│   ├── decisions/         # Key decisions made during development
│   ├── learnings/         # What AI learned about codebase patterns
│   └── handoffs/          # Context for agent transitions
├── context/
│   └── project-summary.md # High-level project understanding
└── .gitignore             # Exclude sensitive/ephemeral files
```

**What Goes Where**:

| Folder | Content | Git Tracked? |
|--------|---------|--------------|
| `memory/session/` | Current task progress, temporary notes | No |
| `memory/decisions/` | Why choices were made, tradeoffs considered | Yes |
| `memory/learnings/` | Codebase patterns, gotchas, conventions | Yes |
| `memory/handoffs/` | Context for next agent session | Yes |
| `context/` | Project overview, architecture summary | Yes |

**Sample `.ai/.gitignore`**:
```
# Ephemeral session data
memory/session/*
!memory/session/.gitkeep

# Sensitive information
secrets/
*.credentials
*.keys
```

**Relationship to AOSD/**:
- `.ai/` stores agent working context (ephemeral and cumulative)
- `AOSD/` stores formal documentation (ADRs, slice specs, patterns)
- Decisions in `.ai/memory/decisions/` may later become formal ADRs in `AOSD/decisions/`
- Both folders live at project root

**When to Create**:
- Working with AI agents regularly
- Multi-agent orchestration scenarios
- Need to reduce context-rebuilding overhead
- Want agents to learn and retain codebase knowledge

---

## `AOSD/context/` (Context Nuggets)

Context Nuggets are focused, reusable documentation modules designed specifically for just-in-time (JIT) loading by AI agents. Unlike full documentation, nuggets are optimized for token efficiency and selective retrieval.

*See also: [AI_GUIDE.md Section 8.2](../AI_GUIDE.md#82-context-nuggets-and-jit-retrieval) for agent loading rules.*

**Purpose**:
- Provide focused context for specific domains or subsystems
- Enable selective loading based on task requirements
- Reduce token waste from loading irrelevant documentation
- Support Change Class-based retrieval (Class A/B/C)

### Context Nugget Standards

| Standard | Requirement |
|----------|-------------|
| **Maximum size** | ≤500 lines per nugget |
| **Scope** | Single domain, subsystem, or concept |
| **Header required** | Standard header template (see below) |
| **No secrets** | Never include credentials, keys, or sensitive data |
| **Self-contained** | Nugget should be useful without reading other nuggets |
| **Cross-references** | Link to related nuggets, not inline them |

### Nugget Header Template

Every context nugget must begin with this header:

```markdown
# Context: [Domain/Subsystem Name]

**Scope**: [What this nugget covers]
**Load When**: [When an agent should load this]
**Change Classes**: [A/B/C - which classes need this]
**Related Nuggets**: [Links to related context files]
**Last Updated**: [YYYY-MM-DD]

---
```

### Context Nuggets and Change Classes

Context nuggets integrate with Change Classes (see DEVELOPMENT_WORKFLOW.md Section 7):

| Change Class | Nugget Loading Rule |
|--------------|---------------------|
| **Class A** (trivial) | No nuggets needed - change is too simple |
| **Class B** (bounded) | Load specific nugget for affected domain |
| **Class C** (architectural) | Load selected nuggets for all affected domains |

### Example Nuggets

**`AOSD/context/README.md`** (Nugget Index):
```markdown
# Context Nuggets Index

This folder contains focused context modules for AI agents.

## Available Nuggets

| Nugget | Scope | Load When |
|--------|-------|-----------|
| [auth-flow.md](./auth-flow.md) | Authentication system | Working on auth-related code |
| [data-model.md](./data-model.md) | Database entities | Modifying data layer |
| [api-patterns.md](./api-patterns.md) | API design conventions | Adding/modifying endpoints |

## How to Use

1. Check Change Class for your task
2. Load relevant nuggets (Class B: specific, Class C: multiple)
3. Reference nuggets in your work, don't duplicate content
```

**`AOSD/context/auth-flow.md`** (Example Nugget):
```markdown
# Context: Authentication Flow

**Scope**: JWT-based authentication, session management, role-based access
**Load When**: Working on login, logout, permissions, or protected routes
**Change Classes**: B (auth changes), C (security architecture)
**Related Nuggets**: [api-patterns.md](./api-patterns.md)
**Last Updated**: 2025-01-15

---

## Authentication Overview

[Concise description of auth flow - aim for 50-100 lines]

## Key Components

- **Token Service** (`src/services/auth/token.ts`): JWT generation and validation
- **Auth Middleware** (`src/middleware/auth.ts`): Request authentication
- **User Repository** (`src/repositories/user.ts`): User data access

## Critical Rules

1. Tokens expire after 24 hours
2. Refresh tokens stored in httpOnly cookies
3. Role checks happen at middleware level, not handler level

## Common Patterns

[2-3 code snippets showing correct patterns]

## Anti-Patterns

[1-2 examples of what NOT to do]
```

**`AOSD/context/data-model.md`** (Example Nugget):
```markdown
# Context: Data Model

**Scope**: Database entities, relationships, access patterns
**Load When**: Adding/modifying entities, changing data access, writing migrations
**Change Classes**: B (entity changes), C (schema migrations, new entities)
**Related Nuggets**: [api-patterns.md](./api-patterns.md)
**Last Updated**: 2025-01-15

---

## Entity Overview

[Concise entity list with key attributes]

## Key Patterns

- **Tenant isolation**: All queries include tenant_id
- **Soft deletes**: Use deleted_at instead of hard delete
- **Audit fields**: created_at, updated_at on all entities

## Access Patterns

[Primary query patterns with examples]

## Migration Rules

1. Always use reversible migrations
2. Test migrations on staging before production
3. Large data migrations require ADR approval
```

### Sensitive Information Guidance

**NEVER include in context nuggets**:
- API keys, tokens, or credentials
- Database connection strings
- Secret environment variables
- PII or sample user data
- Internal URLs for production systems
- Security vulnerability details (reference ADRs instead)

**Instead**:
- Reference where secrets are stored (e.g., "API key in AWS Secrets Manager")
- Use placeholder values (e.g., `YOUR_API_KEY_HERE`)
- Link to secure documentation for sensitive procedures

### When to Create Nuggets

Create a context nugget when:
- A domain is frequently touched by AI agents
- Context is needed for Class B or C changes
- Full documentation is too large for efficient loading
- Multiple agents/sessions need consistent domain knowledge

**Do NOT create nuggets for**:
- Trivial or rarely-changed code areas
- Content already covered in AOSD.md overview
- Temporary or experimental features

---

# Setup Instructions

## For New Applications

1. **Create AOSD/ folder**:
   ```bash
   mkdir -p AOSD/decisions AOSD/slices AOSD/patterns
   ```

2. **Create .ai/ folder** (optional, for AI agent context):
   ```bash
   mkdir -p .ai/memory/session .ai/memory/decisions .ai/memory/learnings .ai/memory/handoffs .ai/context
   touch .ai/memory/session/.gitkeep
   ```

   Create `.ai/.gitignore`:
   ```
   memory/session/*
   !memory/session/.gitkeep
   secrets/
   *.credentials
   ```

3. **Copy template files**:
   - Copy `APP_TEMPLATE.md` → `AOSD/AOSD.md`
   - Create `AOSD/README.md` using template above
   - Create `AOSD/ORCHESTRATION_PROFILE.md` using template above

4. **Fill in templates**:
   - Complete `AOSD.md` with your application details
   - Link to your Orchestration Profile
   - Document any initial decisions in `decisions/`

5. **Add to version control**:
   ```bash
   git add AOSD/ .ai/
   git commit -m "Add AOSD Level 3 documentation structure"
   ```

6. **Update application README**:
   Add section pointing to AOSD documentation:
   ```markdown
   ## AOSD Documentation
   This project uses [AOSD methodology](https://github.com/Algorail-AI/AOSD).
   See [AOSD/](./AOSD/) for application-specific documentation.
   ```

---

## For Existing Applications

1. **Create AOSD/ folder** (as above)

2. **Migrate existing documentation**:
   - Move architecture docs → `AOSD/AOSD.md`
   - Move ADRs → `AOSD/decisions/`
   - Move slice specs → `AOSD/slices/`

3. **Document current state**:
   - Fill in `AOSD.md` based on existing implementation
   - Create retrospective ADRs for major past decisions
   - Link to Orchestration Profile

4. **Communicate change**:
   - Update team on new structure
   - Update AI agent prompts to reference AOSD/
   - Update CI/CD if it references old doc locations

---

# Best Practices

## Keep It Current
- Update `AOSD.md` when architecture changes
- Create ADRs for all significant decisions
- Create slice specs before implementing features
- Review AOSD/ folder during onboarding

## Make It Discoverable
- Link from application README
- Reference in PR templates
- Include in AI agent prompts
- Mention in team documentation

## Keep It Concise
- Don't duplicate Orchestration Profile content
- Link to Level 2 instead of copying
- Focus on what makes THIS app unique
- Use templates to maintain consistency

## Involve AI Agents
- Provide AOSD/ folder as context to AI agents
- Ask AI to create slice specs in slices/
- Have AI draft ADRs for review
- Use AI to keep documentation in sync with code

---

# FAQ

**Q: Should AOSD/ be in .gitignore?**
A: No! This is critical documentation that should be version controlled.

**Q: What if my app doesn't have any decisions yet?**
A: That's fine - keep the decisions/ folder empty until you have your first ADR.

**Q: Can I add additional folders in AOSD/?**
A: Yes, if needed. Examples: `diagrams/`, `scripts/`, `troubleshooting/`

**Q: Should slice specs be deleted after implementation?**
A: No - keep them for historical context and to help AI understand past decisions.

**Q: What if I have multiple Orchestration Profiles (AWS and Azure)?**
A: Document both in `ORCHESTRATION_PROFILE.md` and specify which is primary.

---

**End of AOSD Folder Structure Guide**
