# Model Assignments Reference Implementation
**Example: Claude Code, ChatGPT, Codex, and Constrained Models**
**Copyright Algorail AI, LLC 2025**

---

## Purpose

This document provides a **reference example** of how one organization assigned specific AI models to abstract AOSD agent roles. Use this as inspiration, not prescription.

> **Note**: Model capabilities evolve rapidly. This example reflects assignments that worked in a specific context at a specific time. Your optimal assignments may differ.

---

## Example Role Assignments

| Abstract Role | Assigned Model | Rationale |
|---------------|----------------|-----------|
| **Builder/Implementer** | Claude Code | Strong multi-file reasoning, consistent refactoring, predictable with clear invariants |
| **Architect/Reviewer** | ChatGPT (GPT-4) | High-level reasoning, architectural clarity, excellent edge case identification |
| **Transformer/Synthesizer** | Codex | Fast, deterministic, good at syntax-heavy transformations |
| **Constrained Executor** | Sonnet 4.5 (constrained environments) | Available in restricted environments, capable within limitations |

---

## Claude Code as Builder/Implementer

In this example configuration, Claude Code served as the primary "hands-on keyboard" developer.

### Responsibilities
- Implement features
- Write or update functions, classes, modules
- Perform refactors consistent with invariants
- Generate fast, medium, and integration tests
- Update documentation (docs/ and inline)
- Apply TodoWrite planning before any changes
- Perform local validation and static analysis
- Execute `/pr` workflow for pull requests

### Why Claude Code for This Role
- Multi-file reasoning capability
- Consistent refactoring behavior
- Good with complex patterns
- Can read entire projects
- Predictable when invariants are clear

### Guardrails Applied
Claude was configured to **not**:
- Architect major systems alone
- Modify infrastructure invariants
- Deploy to Prod
- Touch constrained environments without appropriate prompts
- Invent new cloud platform patterns
- Circumvent environment strategy rules

---

## ChatGPT as Architect/Reviewer

ChatGPT (GPT-4) was assigned the senior engineer/solutions architect role.

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

### Why ChatGPT for This Role
- High-level reasoning strength
- Architectural clarity
- Excellent at identifying edge cases
- Ideal for pre-implementation reviews
- Safe for "what if" exploration

### Guardrails Applied
ChatGPT was configured to **not**:
- Write code directly in the repo
- Modify files on development environment
- Execute build/test/deploy commands
- Perform multi-file refactors
- Make environment-specific assumptions without explicit guidance

---

## Codex as Transformer/Synthesizer

Codex excelled at clean, deterministic transformations.

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

### Why Codex for This Role
- Fast execution
- Deterministic output
- Good at syntax-heavy tasks

### Guardrails Applied
Codex was configured to **not**:
- Change architecture
- Introduce new patterns
- Deploy or modify environment code
- Handle multi-file logic
- Bypass factory patterns or bundling rules

---

## Sonnet as Constrained Executor

Sonnet 4.5 was used in constrained environments where:

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

### Prompting Pattern Used
```
Make only the following minimal change to this file.
Do not refactor anything else.
Do not modify other files.
Follow AOSD invariants.
```

### Guardrails Applied
Sonnet was configured to **not**:
- Perform large-scale refactors
- Modify multiple files
- Design systems
- Update infrastructure
- Apply bundling changes
- Work on Prod or other less-constrained environments

---

## Multi-Agent Collaboration Protocol

This workflow demonstrates how these specific models were orchestrated together:

### Step 1 - ChatGPT (Architecture)
- Clarifies requirements
- Produces clean slice spec
- Advises on invariants

### Step 2 - Claude Code (Implementation)
- Writes code
- Uses TodoWrite
- Updates tests
- Updates docs
- Deploys to Dev

### Step 3 - Automated Testing (E2E)
- Run Dev E2E tests
- Validate UI & flows

### Step 4 - ChatGPT (Review)
- Review code diffs
- Identify issues Builder missed
- Recommend architectural fixes

### Step 5 - Claude Code (Fixes)
- Apply changes
- Merge PR

### Step 6 - Production (Deployment)
- Full test suite
- Security checks
- Staging/Prod deploys

### Step 7 - Sonnet (Constrained) when needed
- Minimal isolated changes
- Only in constrained environments

---

## Inter-Agent Communication Examples

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
This small fix must be propagated to other files in Dev (constrained model cannot do it).
```

---

## Adapting This Example

When creating your own model assignments:

1. **Assess your models' strengths** - Each model has different capabilities
2. **Define clear boundaries** - What each agent can and cannot do
3. **Document guardrails** - Explicit constraints prevent drift
4. **Plan collaboration** - How agents hand off work to each other
5. **Review periodically** - Model capabilities change; reassess assignments

See [MODEL_ROLES.md](../../../MODEL_ROLES.md) for the abstract role definitions to assign your chosen models to.

---

**End of Model Assignments Reference Implementation**
