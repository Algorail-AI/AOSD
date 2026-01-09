# Agent Roles
**AOSD Framework**
**Clear Responsibilities for AI Agents in Multi-Agent Orchestration**
**Copyright Algorail AI, LLC 2025**

---

## 1. Purpose

This document defines **abstract agent roles, boundaries, and interactions** for AI-native engineering workflows in AOSD.

Explicit roles keep the system:

- predictable
- safe
- consistent
- secure
- repeatable
- easier to debug

These role definitions are **universal**. You assign your specific AI models to these roles in your Orchestration Profile (Level 2). See [IMPLEMENTATION_TEMPLATE.md](IMPLEMENTATION_TEMPLATE.md) for guidance on model assignment.

> **Reference Implementation**: For an example of how specific models (Claude Code, ChatGPT, Codex) were assigned to these roles, see [examples/reference-implementations/model-assignments/](examples/reference-implementations/model-assignments/).

---

## 1.1 All Roles Operate Under a Human Orchestrator

**Important**: All roles defined in this document are **AI agent roles**. They operate under the direction and oversight of a **Human Orchestrator**.

The Human Orchestrator:

- **Chooses the agents** assigned to each role
- **Defines trust boundaries** for each agent
- **Reviews outputs** at appropriate checkpoints
- **Enforces autonomy limits** based on trust zones (see Principle 8)
- **Resolves conflicts** between agents or when agents are uncertain
- **Maintains final authority** over all decisions

This distinction is critical: while this document may reference an "Architect/Reviewer" agent role, this is separate from the human "Owner / Architect" role defined in [DEVELOPMENT_WORKFLOW.md](DEVELOPMENT_WORKFLOW.md). The human orchestrates; the AI agents execute within defined boundaries.

See **Principle 1 (Multi-Agent Orchestration)** and **Principle 8 (Human-in-the-Loop Oversight)** in [AOSD_FRAMEWORK.md](AOSD_FRAMEWORK.md) for the foundational principles.

---

## 2. Agent Roles Summary Table

| Role | Primary Function | Typical Trust Levels | What It Should Do | What It Should NOT Do |
|------|------------------|---------------------|-------------------|------------------------|
| **Builder/Implementer** | Hands-on development | 3-5 | Write code, refactor, generate tests, update docs | Architect major systems, decide invariants, deploy to Prod |
| **Architect/Reviewer** | Design and oversight | 1-2 | Design systems, review code, catch edge cases | Write multi-file code or execute environment operations |
| **Transformer/Synthesizer** | Structured transformations | 4-5 | Rewrite code, enhance structure, generate boilerplate | Change architecture, modify infra, deploy anything |
| **Constrained Executor** | Minimal changes in restricted environments | 3-4 | Apply tiny edits, fix defects in isolated files | Perform refactors, large changes, multi-file work |

*Trust Levels reference HITL Trust Zones (Principle 8): 0=Full Human, 1=AI Assists, 2=AI Drafts, 3=AI Executes, 4=AI Autonomous, 5=AI Silent*

---

## 3. Builder/Implementer

The Builder is the **primary "hands-on keyboard" developer** for AOSD projects.

### Responsibilities
- Implement features
- Write or update functions, classes, modules
- Perform refactors consistent with invariants
- Generate fast, medium, and integration tests
- Update documentation (docs/ and inline)
- Apply TodoWrite planning before any changes
- Perform local validation and static analysis
- Execute PR workflows for pull requests
- Challenge invariants when context makes them inappropriate (see [INVARIANTS_COOKBOOK Section 10.5](./INVARIANTS_COOKBOOK.md#105-invariant-challenge-protocol))

### Ideal Characteristics
When selecting a model for this role, look for:
- Multi-file reasoning capability
- Consistent refactoring behavior
- Good with complex patterns
- Ability to read entire projects
- Predictable output when invariants are clear

### Limits
The Builder must **not**:
- Architect major systems alone
- Modify infrastructure invariants
- Deploy to Prod without human approval
- Touch constrained environments (if another agent is designated)
- Invent new cloud platform patterns
- Circumvent environment strategy rules

---

## 4. Architect/Reviewer

The Architect is the **senior engineer/solutions architect** for design and oversight.

### Responsibilities
- Clarify user requirements
- Transform ideas into slice specs
- Review Builder agent's output
- Identify architectural risks
- Recommend patterns
- Suggest improved algorithms
- Evaluate invariants
- Spot violations of assumptions
- Generate documentation, diagrams, and workflows
- Evaluate invariant challenges from other agents and provide recommendations to Human Orchestrator (see [INVARIANTS_COOKBOOK Section 10.5](./INVARIANTS_COOKBOOK.md#105-invariant-challenge-protocol))

### Ideal Characteristics
When selecting a model for this role, look for:
- Strong high-level reasoning
- Architectural clarity
- Excellent edge case identification
- Good at "what if" exploration
- Clear communication of tradeoffs

### Limits
The Architect must **not**:
- Write code directly in the repo
- Modify files in any environment
- Execute build/test/deploy commands
- Perform multi-file refactors
- Make environment-specific assumptions without explicit guidance

---

## 5. Transformer/Synthesizer

The Transformer excels at **clean, deterministic transformations**.

### Use Cases
- Rewriting code in a new style
- Adding type hints
- Converting sync to async
- Updating patterns
- Migrating boilerplate
- Creating structured stubs or skeletons

### Responsibilities
- Deterministic code transformations
- Single-file structural updates
- Safe rewrites
- Preparing code for Builder agent refinement

### Ideal Characteristics
When selecting a model for this role, look for:
- Fast execution
- Deterministic output
- Good at syntax-heavy tasks
- Reliable structural transformations

### Limits
The Transformer must **not**:
- Change architecture
- Introduce new patterns
- Deploy or modify environment code
- Handle multi-file logic
- Bypass factory patterns or bundling rules

---

## 6. Constrained Executor

The Constrained Executor operates in **restricted environments** where:

- Context windows are small
- Model capabilities are limited
- Multi-file editing is unsafe
- Security controls are strict
- Operations must be atomic

### Responsibilities
- Make small, local code edits
- Fix bugs in isolated files
- Apply patches that do not ripple across the system
- Work within IDE using tight prompts

### Prompting Pattern
Use constrained prompts:

```
Make only the following minimal change to this file.
Do not refactor anything else.
Do not modify other files.
Follow AOSD invariants.
```

### Ideal Characteristics
When selecting a model for this role, look for:
- Availability in your constrained environment
- Reliable single-file editing
- Ability to follow strict instructions
- Predictable, minimal changes

### Limits
The Constrained Executor must **not**:
- Perform large-scale refactors
- Modify multiple files
- Design systems
- Update infrastructure
- Apply bundling changes
- Work outside designated constrained environments

---

## 7. Multi-Agent Collaboration Protocol

To make all agents work together, AOSD uses a **multi-agent baton-pass workflow**:

### Step 1 - Architect (Design)
- Clarifies requirements
- Produces clean slice spec
- Advises on invariants

### Step 2 - Builder (Implementation)
- Writes code
- Uses TodoWrite
- Updates tests
- Updates docs
- Deploys to Dev

### Step 3 - Automated Testing
- Run Dev E2E tests
- Validate UI & flows

### Step 4 - Architect (Review)
- Review code diffs
- Identify issues Builder missed
- Recommend architectural fixes

### Step 5 - Builder (Fixes)
- Apply changes
- Merge PR

### Step 6 - Production (Deployment)
- Full test suite
- Security checks
- Staging/Prod deploys

### Step 7 - Constrained Executor (when needed)
- Minimal isolated changes
- Only in restricted environments

---

## 8. How AI Agents Should Communicate with Each Other

### Builder to Architect
```
Please review the following implementation for architecture risks and invariant compliance.
```

### Architect to Builder
```
Implement these refinements using TodoWrite and AOSD patterns.
```

### Builder to Transformer
```
Rewrite this file for structure and clarity while preserving behavior.
```

### Transformer to Builder
```
Here is a clean rewrite. Please integrate and update tests.
```

### Constrained Executor to Builder
```
This small fix must be propagated to other files in Dev (constrained environment cannot do it).
```

---

## 9. Summary of Agent Roles

- **Builder/Implementer** = Primary developer, writes and tests code
- **Architect/Reviewer** = Senior engineer, designs and reviews
- **Transformer/Synthesizer** = Structured code transformations
- **Constrained Executor** = Minimal-patch executor for restricted environments

Roles must not overlap unless explicitly directed.

Clear separation ensures:

- predictable behavior
- lower risk
- fewer regressions
- faster debugging
- safer deployments

---

## 10. Assigning Models to Roles

When implementing AOSD, you must assign your chosen AI models to these abstract roles. Document your assignments in your Orchestration Profile (Level 2).

### Considerations for Assignment
1. **Model capabilities** - Match strengths to role requirements
2. **Cost** - Balance capability against API/usage costs
3. **Availability** - Ensure models are accessible in your environments
4. **Consistency** - Choose models with predictable behavior
5. **Evolution** - Plan to reassess as models improve

### Documentation Requirements
For each role assignment, document:
- Which model you've assigned
- Why it's suitable for the role
- Specific guardrails you've configured
- Any customizations to the standard responsibilities

See [IMPLEMENTATION_TEMPLATE.md](IMPLEMENTATION_TEMPLATE.md) Section "AI Agent and Model Selection" for the ADR format to document your choices.

---

**End of Agent Roles**
