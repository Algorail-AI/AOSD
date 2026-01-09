# Claude + ChatGPT Prompting Patterns Reference Implementation

**AOSD Level 2 Example - Prompt Templates for Claude Code, ChatGPT, Codex, and Constrained Models**

> **Note**: This is a reference implementation showing specific prompt templates for Claude Code, ChatGPT, Codex, and constrained models. Adapt these templates for your specific AI toolset. See the generalized [AI Prompting Patterns](../../../AI_PROMPTING_PATTERNS.md) for platform-agnostic principles.

---

## 1. Purpose

This document defines **specific prompting templates** for directing AI tools during software development, architecture, testing, and deployment in an AOSD environment using Claude and ChatGPT.

These templates ensure:

- Clarity
- Stability
- Safety
- Predictability
- Alignment with AOSD architecture invariants
- Correct handling of Dev/Prod environments
- Consistent behavior across AI models

---

## 2. Prompt Patterns for Each AI Model

### 2.1 Claude Code (Primary Builder)

Claude Code is your **main implementation agent**.
It responds best to:

- **Slice specs**
- **Explicit instructions**
- **Multi-step workflows**
- **TodoWrite planning**
- **File-level or project-level context**

#### Claude Prompt Template

```
You are modifying code in the development environment.

Follow these rules:
  * Use TodoWrite before any code changes.
  * Follow AOSD architecture invariants (WAF, AWS Factory, Lambda Bundling, Decision Catalog, etc.).
  * Never call boto3 directly.
  * Never deploy to Prod.
  * Only run Dev deployments via ENV=dev.
  * All code must be compatible with the existing folder structure.

Task:
<task_description_here>

Output:
  * TodoWrite plan
  * Then code changes
  * Then test updates
  * Then documentation updates
```

---

### 2.2 ChatGPT (Architect & Reviewer)

ChatGPT is your **high-level reasoning agent**, not your implementer.
Use it to:

- Preview architecture
- Review Claude's work
- Identify risks
- Suggest patterns
- Split tasks into slices
- Create specs, workflows, templates
- Build test plans
- Improve algorithmic or architectural clarity

#### ChatGPT Prompt Template

```
You are acting as a senior architect reviewing a proposed change.

Please:
  1. Evaluate the design.
  2. Identify risks or invariants it touches.
  3. Recommend improvements.
  4. Provide a refined slice spec.
  5. Explain what Claude Code should implement next.
```

---

### 2.3 Codex (Optional Builder / Fast Synthesizer)

Codex is useful when:

- Code synthesis must be fast
- Refactors are needed on individual files
- You want deterministic transformation
- You want boilerplate or glue code generated quickly

#### Codex Prompt Template

```
Rewrite this code to follow the AOSD AWS Factory and Lambda Bundling patterns.
Preserve behavior. Improve structure where possible.
Do not modify environment rules or deployment logic.
```

---

### 2.4 Constrained Models

Some models in constrained environments are limited:

- Smaller context window
- Limited multi-file reasoning
- Fewer advanced coding capabilities
- Often needs task slicing
- Ideal for small, atomic changes

#### Constrained Model Prompt Template

```
You are operating in a constrained environment.

Task:
Make ONLY the following modification to the file shown.

Constraints:
  * Do NOT refactor unrelated code.
  * Do NOT introduce new dependencies.
  * Do NOT touch other files.
  * Keep changes minimal and precise.
  * Follow established factory and bundling patterns.
  * Preserve all existing invariants.
```

This style helps constrained models avoid over-expansion.

---

## 3. Task-Specific Prompt Templates

### 3.1 Refactor Tasks

Use this when updating structure but not intent:

```
Refactor this code to follow:
  * AWS Factory pattern
  * Lambda Bundling invariant
  * Multi-tenant DynamoDB pattern

Do not change behavior or output shape.
```

---

### 3.2 Feature Implementation Tasks

Use this when introducing new logic:

```
Implement this feature using TodoWrite.

Constraints:
  * Maintain architecture invariants.
  * Follow slice spec.
  * Update tests.
  * Update docs.
  * Deploy only to Dev after implementation.
```

---

### 3.3 Test Development Tasks

```
Write tests for the following behaviors.

Only generate:
  * Fast tests for pure functions
  * Medium tests for mocked AWS
  * Integration tests marked @pytest.mark.aws
```

---

### 3.4 Documentation Tasks

```
Generate documentation in Markdown for the following feature.
Follow AOSD's documentation tone and structure.
```

---

## 4. Multi-Agent Workflow Prompts

When coordinating Claude + ChatGPT:

1. **ChatGPT shapes architecture & slice spec**
2. **Claude Code implements**
3. **ChatGPT reviews**
4. **Claude Code applies fixes**
5. **Dev EC2 runs tests + deploy**
6. **Laptop runs Playwright**

Explicitly tell each agent their role.

---

## 5. Task Slicing Prompts

Large tasks should be broken into:

- Part A: Data model
- Part B: API layer
- Part C: Lambda logic
- Part D: UI changes
- Part E: Tests
- Part F: Documentation

Include this structure when giving Claude large assignments.

---

## 6. Anti-Patterns (Never Do These)

AI must NEVER:

- Create or modify AWS access keys
- Suggest SSH access
- Write code to the laptop
- Copy Prod data to Dev or laptop
- Disable WAF or IAM protections
- Use boto3 directly
- Invent new infrastructure patterns
- Deploy to Prod without explicit human approval
- Run destructive Playwright tests in Prod
- Generate environment variables containing secrets
- Circumvent AOSD invariants

---

## Related Documents

- [AI Prompting Patterns (Generalized)](../../../AI_PROMPTING_PATTERNS.md) - Platform-agnostic principles
- [AOSD Framework](../../../AOSD_FRAMEWORK.md) - Core methodology
- [AWS + Claude Workflow](./aws-claude-workflow.md) - Full workflow example

---

**End of Claude + ChatGPT Prompting Patterns Reference Implementation**
