# AI Guide for AOSD

**For AI Agents Helping Humans Apply AOSD**

This document is written specifically for **AI agents** (Claude Code, ChatGPT, Amazon Q, Cursor, Copilot, etc.) that are helping humans adopt and apply the **AI Orchestrated Systems Development (AOSD)** methodology.

> **Note**: This guide is for applying AOSD methodology to build systems. For AI agents operating on the **AOSD Framework repository itself**, read [AGENT.md](AGENT.md) for repo-specific operator guidance.

Your goals as an AI agent are to:

- **Minimize token usage** when reading AOSD documentation
- **Extract and apply patterns** consistently
- **Guide humans through AOSD** end-to-end
- **Reference principles and goals** accurately

---

## 1. Repository Structure Overview

```
AOSD/                               # AOSD Framework (Level 1)
├── README.md                       # Human-facing overview
├── AGENT.md                        # Tool-agnostic repo operator contract
├── AI_GUIDE.md                     # This file - methodology guide
├── CHANGELOG.md                    # Version history
├── AOSD_INFLUENCES_AND_ACKNOWLEDGMENTS.md  # Non-normative: influences, citations
├── MANUAL/
│   ├── AOSD_FRAMEWORK.md           # Core methodology (KEY DOCUMENT)
│   ├── MATURITY_MODEL.md           # Adoption levels (extracted from framework)
│   ├── ARCHITECTURE_PATTERNS.md    # Reference patterns (extracted from framework)
│   ├── IMPLEMENTATION_TEMPLATE.md  # For creating Level 2 profiles
│   ├── TROUBLESHOOTING.md          # AI issues, drift, teams, restricted environments
│   ├── DEVELOPMENT_WORKFLOW.md     # Workflow principles
│   ├── ENVIRONMENT_STRATEGY.md     # Environment isolation
│   ├── AI_PROMPTING_PATTERNS.md    # Prompting principles
│   ├── MODEL_ROLES.md              # AI agent role definitions
│   └── examples/reference-implementations/
├── TEMPLATES/                      # Reusable templates
└── SYLLABUS/                       # Training curriculum
```

**Key insight**: AOSD has three levels:
- **Level 1** (this repo): Universal, platform-agnostic framework
- **Level 2**: Organization-specific Orchestration Profile (separate repo)
- **Level 3**: Application-specific AOSD folder (in each app repo)

AI agents should primarily work with Level 1 patterns unless explicitly instructed to reference a Level 2 profile.

---

## 2. Token-Efficient Reading Strategy (*Critical*)

**Do NOT read the entire AOSD manual into context by default.**

Instead, load sections on demand based on what the human needs:

### Quick Reference: What to Load When

| Human's Question | Load These Sections |
|------------------|---------------------|
| "What is AOSD?" / "Why use it?" | AOSD_FRAMEWORK.md: "Why AOSD Exists" + Goals 1-6 |
| "How do we structure work?" | Slice-Based Development, Work Item Lifecycle, TodoWrite Workflow |
| "How do we keep this secure?" | Trustworthy-by-Design, Environment Isolation, Security & Compliance Principles |
| "What invariants should we start with?" | Architectural Invariants → Starter Invariants (S1-S2, O1-O2, T1-T2, SF1-SF2) |
| "How do we organize branches?" | Version Control and Branching, Work Item Lifecycle |
| "How does this scale to a team?" | Scaling AOSD for Teams and Organizations |
| "How do we manage AI costs?" | Cost Metrics, DEVELOPMENT_WORKFLOW.md Section 8 (Cost Attribution), AI_PROMPTING_PATTERNS.md Section 10 |
| "What if we're in a restricted environment?" | AOSD in Restrictive Environments |
| "How do we test?" | Testing Strategy (Fast/Medium/Slow) |
| "What patterns should we follow?" | MANUAL/ARCHITECTURE_PATTERNS.md (extracted file) |
| "How much planning is needed?" | TodoWrite Workflow, Risk-Based TodoWrite Tiers, Change Classes (Section 7) |
| "How do I create an Orchestration Profile?" | IMPLEMENTATION_TEMPLATE.md, AI_GUIDE Section 8.3 (Quick Start), examples/level-2/ |
| "How should we structure our AOSD repo?" | IMPLEMENTATION_TEMPLATE.md: Recommended Repository Structure (upstream/org/project separation) |
| "How do I set up AOSD for an app?" | TEMPLATES/APP_TEMPLATE.md, TEMPLATES/AOSD_FOLDER_STRUCTURE.md |
| "How do agents share context?" | Pattern 7: Agent Context Memory Files, AI_GUIDE Section 8.1 |
| "What context do I need for this task?" | AI_GUIDE Section 8.2 (Context Nuggets and JIT Retrieval), AOSD/context/ folder |
| "How do I run multiple agents in parallel?" | MANUAL/PARALLEL_DEVELOPMENT.md, MANUAL/MULTI_AGENT_PATTERNS.md |
| "How do I manage AI sessions?" | MANUAL/DEVELOPMENT_WORKFLOW.md Section 6 (AI Session Management) |
| "How do agents communicate?" | MANUAL/AGENT_COMMUNICATION.md |
| "How do we manage AI model versions?" | MANUAL/MODEL_GOVERNANCE.md |
| "How do we keep docs consistent?" | MANUAL/DOCUMENTATION_GOVERNANCE.md, TEMPLATES/CONSISTENCY_CHECK_SLICE.md |
| "How do we track AI changes?" / "What metadata should we capture?" | AI Provenance (Principle 6), Evidence Packages |
| Troubleshooting emerging patterns | MANUAL/LESSONS_LEARNED.md (optional, non-authoritative working memory) |

### Reading Priority Order

When helping a human new to AOSD:

1. **First**: "Why AOSD Exists" (Goals 1-6) - establishes context
2. **Second**: Core Principles overview (skim the 12 principles)
3. **Then**: Load specific sections based on their immediate needs

**Rule of thumb**: Ask the human what they're trying to accomplish, then load only the relevant section.

### What NOT to Load by Default

To save tokens and prevent context bloat, avoid loading these entirely:

- `MANUAL/AOSD_FRAMEWORK.md` (full file) - load specific sections instead
- `MANUAL/IMPLEMENTATION_TEMPLATE.md` (full file) - only when creating Level 2 profiles
- `MANUAL/TROUBLESHOOTING.md` (full file) - only when debugging issues (includes integrated AI features, agent drift, team workflows, restricted environments)
- `MANUAL/examples/` folder - only when human asks for examples
- `SYLLABUS/` folder - only when human is learning AOSD concepts

### Anchor-Based Navigation

AOSD_FRAMEWORK.md includes anchor comments to help AI agents locate sections efficiently without reading the entire file.

**Available Anchors**:
- `<!-- AOSD:SECTION:CORE_PRINCIPLES -->` - The 12 core principles
- `<!-- AOSD:SECTION:MATURITY_MODEL -->` - Adoption levels (stub; full content in MATURITY_MODEL.md)
- `<!-- AOSD:SECTION:ARCHITECTURE_PATTERNS -->` - Reference patterns (stub; full content in ARCHITECTURE_PATTERNS.md)
- `<!-- AOSD:SECTION:STARTER_INVARIANTS -->` - Day-1 invariants for new adopters
- `<!-- AOSD:SECTION:RUNTIME_INVARIANTS -->` - Invariants validated during operation
- `<!-- AOSD:SECTION:EVIDENCE_PACKAGES -->` - Structured artifacts proving change safety

**How to Use Anchors**:

1. **Find anchors in the file**:
   ```
   grep "<!-- AOSD:SECTION:" MANUAL/AOSD_FRAMEWORK.md
   ```

2. **Navigate to specific section**: Search for the anchor pattern, then read from that point.

3. **Extracted content**: For Maturity Model and Architecture Patterns, load the dedicated files (`MATURITY_MODEL.md`, `ARCHITECTURE_PATTERNS.md`) for full content.

**Pattern for discovering anchors**:
```
grep -n "<!-- AOSD:SECTION:.*-->" MANUAL/AOSD_FRAMEWORK.md
```

---

## 3. AOSD Core Concepts (Quick Reference)

### The Six High-Level Goals

1. **Sustainability** - Systems outlive their original developers
2. **Security** - Trustworthiness engineered from the start
3. **Fast Evolution** - Development and changes remain fast and safe
4. **Low Cost** - Lean, cloud-native, minimal operational overhead
5. **AI Runtime** - AI as first-class application capability
6. **Embedded Training** - Applications train their users directly

### The Twelve Core Principles

1. Multi-Agent Orchestration
2. Trustworthy-by-Design Architecture
3. Environment Isolation
4. Architectural Invariants
5. Slice-Based Development
6. TodoWrite Workflow
7. Testing Strategy (Fast/Medium/Slow)
8. Human-in-the-Loop Oversight
9. Functional Core, Imperative Shell (FCIS)
10. Version Control and Branching
11. Work Item Lifecycle
12. Self-Documenting Systems

### Key AOSD Terminology

| Term | Meaning |
|------|---------|
| **Orchestrator** | Human who directs AI agents |
| **Slice** | Vertical feature delivering end-to-end value |
| **Invariant** | Non-negotiable architectural rule |
| **Build-Time Invariant** | Invariant validated during build, test, or deployment |
| **Runtime Invariant** | Invariant validated continuously during operation (see Principle 4) |
| **Starter Invariant** | Canonical minimal invariant from the starter set (S1-S2, O1-O2, T1-T2, SF1-SF2) — safe defaults for new adopters |
| **Evidence Package** | Structured artifact proving due diligence for a change (see Principle 6) |
| **AI Provenance** | Metadata about AI-assisted changes enabling traceability (see Principle 6) |
| **Builder (Implementer) Agent** | AI that implements code |
| **Reviewer Agent** | AI that reviews architecture/code |
| **Trust Zone** | Area with defined autonomy level (see HITL Trust Levels below) |
| **Risk Tier** | Planning/oversight level: High, Medium, Low, Zero (see Risk Tiers below) |
| **Work Item** | Trackable unit of work (issue, ticket) |
| **Orchestration Profile** | Level 2 environment-specific implementation |
| **Extension/Overlay** | Organization's additions layered over upstream AOSD (never modify upstream) |

### HITL Trust Levels (Principle 8)

| Level | Name | AI Autonomy | Human Role |
|-------|------|-------------|------------|
| 0 | Full Human | None | Does everything |
| 1 | AI Assists | Suggestions only | Decides and executes |
| 2 | AI Drafts | Creates artifacts | Reviews and approves |
| 3 | AI Executes | Runs with approval | Approves before execution |
| 4 | AI Autonomous | Full autonomy | Post-hoc review |
| 5 | AI Silent | Full autonomy | No review needed |

Use these levels to classify operations in slice specs and work items.

### Risk-Based TodoWrite Tiers (Principle 6)

| Tier | Risk Level | Examples | HITL Requirement | TodoWrite |
|------|------------|----------|------------------|-----------|
| **High** | Security, data, production | IAM policies, DB migrations, auth changes | Full review required | Mandatory |
| **Medium** | Business logic, integrations | API endpoints, service calls, handlers | Spot check | Recommended |
| **Low** | UI, tests, documentation | Component styling, unit tests, comments | Automated only | Optional |
| **Zero** | Generated, trivial | Boilerplate, imports, formatting, typos | None | Skip |

Use these tiers to determine planning effort and oversight level before starting work.

---

## 4. How to Guide Humans Through AOSD

### Step 1: Understand Their Context

Ask or determine:
- **Team size**: Solo, small (2-5), medium (6-15), enterprise (15+)?
- **Project state**: New system, existing system, or rescue/refactor?
- **Environment**: Which cloud platform? Any restrictions?
- **Maturity**: New to AOSD or already practicing?

### Step 2: Select Relevant Guidance

Based on context, emphasize:

| Context | Emphasize |
|---------|-----------|
| Solo developer | TodoWrite discipline, AI as reviewer, documentation habits |
| Small team | Shared invariants, branch ownership, coordination patterns |
| Restricted environment | AOSD in Restrictive Environments section |
| Security-critical | Trustworthy-by-Design, Environment Isolation |
| Cost-sensitive | Cost metrics, serverless-first patterns |
| Legacy rescue | Slice-based incremental approach, invariants |

### Step 3: Propose Concrete Actions

When helping humans adopt AOSD:

1. **Define initial slices** - Break their first feature into vertical slices
2. **Document invariants** - Help them write 5-10 non-negotiable rules
3. **Set up work tracking** - GitHub Issues, or their preferred system
4. **Establish branch strategy** - Feature branches, PR reviews
5. **Create their Level 2 profile** - Use IMPLEMENTATION_TEMPLATE.md

### Step 4: Apply AOSD as Constraints

When suggesting solutions:
- Check against relevant principles
- If a suggestion conflicts with an invariant, say so explicitly
- Help the human decide: follow the principle, adapt it, or intentionally diverge

---

## 5. How to Reference AOSD in Responses

### Use AOSD Language Consistently

When guiding humans, use proper terminology:
- "slice" not "feature" or "task"
- "orchestrator" not "developer" (when in AOSD context)
- "invariant" not "rule" or "constraint"
- "work item" not "ticket" (though both are acceptable)

### Reference Goals and Principles by Number

Good examples:
- "This relates to **Principle 5 (Slice-Based Development)**..."
- "**Goal 2 (Security)** suggests we should..."
- "The **Work Item Lifecycle (Principle 11)** requires..."

### Summarize, Don't Quote Extensively

- Extract the key point from a section
- Apply it to the human's specific situation
- Avoid pasting large blocks of framework text

---

## 6. Formatting Conventions in AOSD Docs

The manual uses consistent patterns you can rely on:

### Goals Format
```
## Goal N — Title
[Description of the goal]
```

### Principles Format
```
## N. Principle Name

**Supports Goals**: [list of goal numbers]

**Principle**: [one-sentence statement]

**Key Concepts**:
- [concept 1]
- [concept 2]

**Implementation Requirements**:
- [requirement 1]
- [requirement 2]
```

### Patterns Format
```
## Pattern N: Name

**Problem**: [what issue this solves]

**Solution**: [how to solve it]

**Implementation Notes**:
- [note 1]
- [note 2]
```

---

## 7. Anti-Patterns to Avoid

### As an AI Agent, Do NOT:

| Anti-Pattern | Why It's Wrong | Better Approach |
|--------------|----------------|-----------------|
| Load entire manual for every question | Wastes tokens, slow | Load specific sections on demand |
| Ignore principles when suggesting solutions | Undermines AOSD value | Check suggestions against principles |
| Invent new "AOSD rules" | Confuses humans, causes drift | Only cite what's in the docs |
| Skip asking about context | Generic advice is less useful | Ask about team size, environment, goals |
| Quote extensively instead of summarizing | Wastes tokens, less helpful | Extract key points, apply to situation |
| Treat AOSD as optional suggestions | Principles are meant to constrain | Apply as constraints, note when diverging |

---

## 8. Templates Quick Reference

When humans need templates, point them to:

| Need | Template |
|------|----------|
| Setting up AOSD for an app | `TEMPLATES/APP_TEMPLATE.md` |
| Creating a slice specification | `TEMPLATES/SLICE_SPEC_TEMPLATE.md` |
| Recording an architecture decision | `TEMPLATES/ADR_TEMPLATE.md` |
| Structuring the AOSD folder | `TEMPLATES/AOSD_FOLDER_STRUCTURE.md` |
| Planning with TodoWrite | `TEMPLATES/TODO_WRITE_TEMPLATE.md` |
| Creating an Orchestration Profile | `MANUAL/IMPLEMENTATION_TEMPLATE.md` |
| Level 2 profile examples | `MANUAL/examples/reference-implementations/level-2/` |
| Setting up agent memory files | `TEMPLATES/AOSD_FOLDER_STRUCTURE.md` (see `.ai/` section) |
| Creating context nuggets | `TEMPLATES/AOSD_FOLDER_STRUCTURE.md` (see `context/` section) |

---

## 8.1 Agent Context Memory Files

AOSD supports persistent AI context via the `.ai/` folder pattern (see Pattern 7 in AOSD_FRAMEWORK.md).

### As an AI Agent, You Should:

**On Session Start**:
1. Check if `.ai/` folder exists in the project
2. If exists, read:
   - `.ai/context/project-summary.md` for project overview
   - `.ai/memory/handoffs/` for context from previous agent sessions
   - `.ai/memory/learnings/` for codebase patterns and conventions
   - `.ai/memory/decisions/` for key decisions made

**During Session**:
- Note significant learnings about the codebase
- Record decisions and their rationale
- Track patterns you discover

**On Session End** (when appropriate):
- Write handoff notes to `.ai/memory/handoffs/`
- Update learnings if you discovered important patterns
- Record any decisions that future agents should know about

### Memory File Guidelines

| Type | When to Write | Content Examples |
|------|---------------|------------------|
| **Handoffs** | End of significant work | "Completed auth refactor. Open: API rate limiting needs review" |
| **Decisions** | After making architectural choices | "Chose Zod over Yup for validation because of TypeScript inference" |
| **Learnings** | When discovering codebase patterns | "All API routes use middleware chain: auth → validate → handle" |
| **Project Summary** | When context changes significantly | Updated architecture overview, key patterns |

### Relationship to AOSD/ Folder

| `.ai/` | `AOSD/` |
|--------|---------|
| Working context (ephemeral + cumulative) | Formal documentation |
| Agent-to-agent communication | Human-facing decisions |
| Learnings may be informal | ADRs follow formal template |
| Quick notes acceptable | Structured content expected |

**Promotion**: Significant decisions in `.ai/memory/decisions/` may warrant formal ADRs in `AOSD/decisions/`.

### Friction Capture (Framework Evolution Signal)

As you work, you may encounter moments where AOSD principles or workflows create unexpected resistance. This friction is **valuable signal** for framework evolution.

**During Session**:
- Notice when an invariant blocks a pattern that seems valid
- Notice when TodoWrite feels too heavy for the actual risk
- Notice when guidance is ambiguous or conflicting
- Notice when you fight the framework rather than use it

**In Handoffs** (optional, free-form):
- Include a brief "Friction / Resistance" note if you encountered significant friction
- Example: *"Friction: Starter invariant S2 (least privilege) made local testing awkward—had to request broader dev permissions than felt right."*

**When Friction Is Moderate**:
- Suggest capturing in [LESSONS_LEARNED.md](MANUAL/LESSONS_LEARNED.md) for tracking
- This is working memory for patterns not yet stable enough for formal guidance
- Lessons must graduate or archive within 30 days

**When Friction Is Severe**:
- If friction is repeated or blocking, suggest the human open a **Framework Stress Event** issue
- This is the mechanism for pull-based framework evolution
- See: `.github/ISSUE_TEMPLATE/framework_stress_event.md`

**Important**: You are capturing signal, not proposing framework changes. Humans decide whether friction warrants action. LESSONS_LEARNED.md entries are non-authoritative—they cannot be cited as guidance.

---

## 8.2 Context Nuggets and JIT Retrieval

Context Nuggets (`AOSD/context/`) are focused documentation modules optimized for just-in-time (JIT) loading by AI agents.

*See also: [AOSD_FOLDER_STRUCTURE.md](TEMPLATES/AOSD_FOLDER_STRUCTURE.md#aosdcontext-context-nuggets) for nugget creation standards and examples.*

### Context Nugget Standards

| Standard | Requirement |
|----------|-------------|
| **Maximum size** | ≤500 lines per nugget |
| **Scope** | Single domain, subsystem, or concept |
| **Header required** | Standard header with Scope, Load When, Change Classes |
| **No secrets** | Never include credentials, keys, or sensitive data |
| **Self-contained** | Useful without reading other nuggets |

### JIT Retrieval Rules for Agents

As an AI agent, follow these rules for loading context nuggets:

**Before Starting Work**:
1. Determine the Change Class (A, B, or C) for the task
2. Check if `AOSD/context/` folder exists
3. If exists, read `AOSD/context/README.md` for the nugget index
4. Load nuggets based on Change Class (see below)

**Change Class → Nugget Loading**:

| Change Class | What to Load | Example |
|--------------|--------------|---------|
| **Class A** (trivial) | No nuggets | Typo fix - just make the change |
| **Class B** (bounded) | Specific nugget for affected domain | Auth bug → load `auth-flow.md` only |
| **Class C** (architectural) | Selected nuggets for all affected domains | New feature → load relevant subset |

**Loading Decision Flow**:
```
1. Is this Class A? → No nuggets needed
2. Is this Class B? → Load ONE nugget matching the affected domain
3. Is this Class C? → Load nuggets for each affected domain (but not all)
```

**Important**: Never load ALL nuggets at once. This defeats the purpose of JIT retrieval.

### Nugget Header Interpretation

Every nugget has a header telling you when to load it:

```markdown
# Context: Authentication Flow

**Scope**: JWT-based authentication, session management
**Load When**: Working on login, logout, permissions
**Change Classes**: B (auth changes), C (security architecture)
**Related Nuggets**: [api-patterns.md](./api-patterns.md)
```

Use the **Load When** field to decide if this nugget is relevant to your current task.

### When to Suggest Creating Nuggets

Suggest creating a context nugget when:
- You notice repeated re-explanation of the same domain
- A Class B or C change requires domain context that doesn't exist
- The human mentions the domain is frequently modified
- Loading full documentation wastes tokens for targeted tasks

### Sensitive Information Rules

**Never load or suggest loading** nuggets that appear to contain:
- Credentials or API keys (even placeholders)
- Database connection strings
- Environment-specific secrets
- PII or real user data

If you encounter sensitive data in a nugget, alert the human and do not process the content.

---

## 8.3 Creating Your First Orchestration Profile (Quick Start)

When helping a human create their first Level 2 Orchestration Profile, follow this step-by-step guide.

### Prerequisites Check

Before starting, verify:
- [ ] Human has read AOSD_FRAMEWORK.md (at least the Goals and Core Principles)
- [ ] Human knows their cloud platform (AWS, Azure, GCP, or other)
- [ ] Human has chosen their primary AI agents (Builder, Reviewer)
- [ ] Human has a repository for the profile (or is ready to create one)

### Step 1: Create Profile Repository

See [IMPLEMENTATION_TEMPLATE.md: Recommended Repository Structure](MANUAL/IMPLEMENTATION_TEMPLATE.md#-recommended-repository-structure) for detailed guidance on structuring your repository.

**Recommended structure** (for organizations):

```
{org-name}-aosd/
├── AOSD/                    # Upstream AOSD (submodule, subtree, or copy)
├── ORG_PROFILE/
│   ├── ORCHESTRATION_PROFILE.md
│   └── INVARIANTS.md
├── README.md
└── CHANGELOG.md
```

**Minimal structure** (for individuals/small teams):

```
{your-name}-aosd/
├── AOSD/                    # Upstream AOSD
├── ORCHESTRATION_PROFILE.md
├── INVARIANTS.md
└── CHANGELOG.md
```

**Action**: Create repository, include upstream AOSD (pinned to a version), and copy [IMPLEMENTATION_TEMPLATE.md](MANUAL/IMPLEMENTATION_TEMPLATE.md) as your profile.

**Critical invariant**: Never modify files in the upstream AOSD directory. Extensions are always additive.

### Step 2: Complete Decision Points Checklist

The template includes a Decision Points Checklist. Guide the human through each decision:

**Core Infrastructure** (5 minutes):
1. Which cloud platform? → Document in Cloud Platform section
2. Where does development happen? → Document in Development Environment section
3. Where does code live? → Document in Source Code Location section

**AI Agents** (5 minutes):
1. Which Builder agent? → Document in Agent Role Mapping
2. Which Reviewer agent? → Document in Agent Role Mapping
3. Separate Transformer? → Usually "Same as Builder" for starters

**Environments** (5 minutes):
1. How many environments? → Document in Environment Strategy
2. How do they promote? → Document Promotion Workflow

### Step 3: Define Initial Invariants

**Critical**: Do not skip this step. Without invariants, AI agents have no constraints.

Start with AOSD Starter Invariants (minimum 5-10 rules):

| Category | Starter | Adapt For Your Context |
|----------|---------|------------------------|
| Security | S1: Encrypt sensitive data | Define what "sensitive" means for you |
| Security | S2: Least privilege access | Define your IAM policy standards |
| Operational | O1: Monitor all services | Define your monitoring stack |
| Operational | O2: Log retention policy | Define retention by environment |
| Tenant | T1: Validate tenant ID | Define your tenant isolation approach |
| Tenant | T2: Tenant ID in keys | Define your data key patterns |
| Factory | SF1: Use service factory | Define your factory location |
| Factory | SF2: Environment config | Define your config approach |

See [invariants-example.md](MANUAL/examples/reference-implementations/level-2/invariants-example.md) for detailed examples.

### Step 4: Configure Agent Context

Create agent configuration that AI agents can reference:

```markdown
# agents/builder-config.md

## Role
Primary Builder/Implementer for day-to-day development.

## Model
Default: [your-model]
Escalate to: [powerful-model] for architecture, security

## Always Read Before Starting
1. Application's AOSD/INVARIANTS.md
2. Relevant slice specification
3. This profile's invariants section

## Non-Negotiable Rules
[Copy your key invariants here for quick reference]
```

See [agent-configuration-example.md](MANUAL/examples/reference-implementations/level-2/agent-configuration-example.md) for detailed examples.

### Step 5: Set Up Workflows

If using GitHub Actions, start with:

1. **CI Workflow**: Run tests on push/PR
2. **Deploy Dev**: Auto-deploy to dev on merge
3. **Invariant Check**: Validate invariants on PR

See [workflow-example.md](MANUAL/examples/reference-implementations/level-2/workflow-example.md) for complete examples.

### Step 6: Connect to Applications

For each application using this profile:

1. Create `AOSD/` folder in the application
2. Reference the profile in `AOSD/README.md`:
   ```markdown
   Orchestration Profile: [link to your profile repo]
   ```
3. Document app-specific invariants in `AOSD/INVARIANTS.md`
4. Create slice specs for features

### Minimum Viable Profile

For a quick start, the minimum viable profile includes:

```
my-aosd/
├── README.md              # One-paragraph overview
├── ORCHESTRATION_PROFILE.md  # Filled sections:
│   ├── Cloud Platform (one decision)
│   ├── AI Agents (Builder + Reviewer)
│   ├── Invariants (5-10 rules)
│   └── Quick Reference for AI Agents
└── CHANGELOG.md           # Version 1.0
```

Everything else can be added incrementally as patterns emerge.

### Common First-Profile Mistakes

| Mistake | Why It's Wrong | Fix |
|---------|---------------|-----|
| No invariants | AI agents have no constraints | Add at least 5-10 starter invariants |
| Too much detail | Hard to maintain, never read | Start minimal, add as needed |
| Copied examples verbatim | Doesn't fit your context | Understand then adapt |
| No agent instructions | Agents don't know the rules | Add Quick Reference section |
| No version tracking | Can't track changes | Add CHANGELOG.md from start |

### For AI Agents Helping Create Profiles

When a human asks for help creating a profile:

1. **Ask first**: What cloud platform? What AI agents? Solo or team?
2. **Provide template**: Point to IMPLEMENTATION_TEMPLATE.md
3. **Guide decisions**: Work through Decision Points Checklist
4. **Emphasize invariants**: This is the critical section
5. **Start minimal**: Don't overwhelm with all sections
6. **Reference examples**: Link to level-2/ examples for inspiration

**Do NOT**:
- Generate a complete profile without asking questions
- Skip the invariants section
- Copy examples without explaining them
- Add sections the human hasn't decided on

---

## 9. Maturity Model Quick Reference

AOSD adoption happens in levels (0-3):

| Level | Name | Key Characteristics |
|-------|------|---------------------|
| 0 | Minimal | Single agent, minimal rules, prototypes and experiments |
| 1 | Foundational | Single agent, basic invariants, slice specs, simple branching |
| 2 | Structured | Role-specific prompts, full test tiers, environment isolation, CI/CD |
| 3 | Advanced | Multi-agent orchestration, ephemeral environments, automated recovery |

For full maturity model details including advancement criteria and assessment questions, see `MANUAL/MATURITY_MODEL.md`.

---

## 10. When Humans Ask Meta Questions

### "Is this the right approach for AOSD?"

Check their proposal against:
1. Relevant principles (does it violate any?)
2. Their documented invariants (if they have them)
3. The appropriate section of the framework

### "Should we diverge from AOSD here?"

Help them think through:
1. Which principle would they be diverging from?
2. What's the tradeoff?
3. Should they document this as an intentional decision (ADR)?

### "How do we know if we're doing AOSD right?"

Point them to:
1. Maturity Model (are they at the level they expect?)
2. Their invariants (are they being followed?)
3. Slice completion rate (are slices delivering value?)

### When to Ask for Clarification

**Ask before assuming** when you encounter:
- Ambiguous or missing platform/environment information
- Unclear slice boundaries or scope
- Unknown team size or ownership structure
- Missing or undocumented invariants
- Unclear security requirements or posture
- Conflicting requirements that need human decision

Do not guess at these — ask the human to clarify.

### When NOT to Generate Large Outputs

**Do not generate** full architectures, large designs, or extensive code until:
- Invariants are defined (at least 5-10 non-negotiable rules)
- Initial slices are specified
- The human has confirmed the scope

Premature large-scale generation leads to drift and wasted effort.

---

## Summary

**Your job as an AI agent** is to help humans apply AOSD effectively by:

1. **Loading documentation efficiently** - sections on demand, not everything at once
2. **Using AOSD language consistently** - slices, orchestrators, invariants, principles
3. **Referencing goals and principles by number** - "Principle 5", "Goal 2"
4. **Applying AOSD as constraints** - not just suggestions
5. **Guiding end-to-end** - from understanding context to concrete actions

When in doubt, ask the human what they're trying to accomplish, then load the relevant section and apply it to their situation.

---

**AOSD Rule for AI Agents**: When AOSD principles, goals, or invariants conflict with your default behavior, **AOSD takes precedence.**

---

**End of AI_GUIDE.md**
