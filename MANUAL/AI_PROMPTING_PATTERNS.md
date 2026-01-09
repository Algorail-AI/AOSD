# AI Prompting Patterns

**AOSD Framework - Best Practices for Communicating with AI Agents**

---

## 1. Purpose

This document defines **prompting patterns** for directing AI agents during software development, architecture, testing, and deployment in AOSD projects.

These principles ensure:

- Clarity
- Stability
- Safety
- Predictability
- Alignment with AOSD architecture invariants
- Correct handling of environment boundaries
- Consistent behavior across different AI models

These patterns are **platform-agnostic** and apply to any AI agent toolset.

---

## 2. Core Principles of AI Prompting in AOSD

### 2.1 Make Context Explicit

AI agents perform best with clarity. Every prompt should include:

- **What you want** - The specific task or outcome
- **Where it applies** - File paths, components, or scope
- **Constraints** - Rules that must be followed
- **Forbidden operations** - Actions that must never happen
- **Desired format** - Expected output structure

**Example:**

> "Modify the file `src/services/auth.py` in the development environment.
> Follow the service factory pattern and environment isolation principles.
> Do not deploy or touch production."

---

### 2.2 Always Reference Invariants

AI must follow the architecture invariants defined in your documentation:

- Architecture invariants document
- Environment strategy
- Pattern library

Prompts should explicitly remind AI of these constraints when safety matters.

---

### 2.3 Favor Declarative Requests Over Imperative

**Avoid:**

> "Change this function to call the database directly."

**Prefer:**

> "Refactor this function to use the service factory pattern.
> Never call the database directly from handlers."

Declarative prompts communicate intent and constraints, not just mechanics.

---

### 2.4 Use Structured Commands

AI agents perform best when given **explicit roles, tasks, and deliverable formats**.

**Example:**

```
Task: Implement Slice 3d changes for client-scoped versions.
Rules:
  * Use TodoWrite first.
  * Follow data isolation patterns.
  * Use ULID for version IDs.
```

---

### 2.5 Use "AI Contracts" to Control Output

AI Contracts define what is allowed and forbidden in the output:

```
AI Contract:
  * Do not deploy to production.
  * Do not modify security policies unless required.
  * Use service factory for all clients.
  * Run TodoWrite first.
  * Follow all architecture invariants.
```

This prevents unwanted operations.

---

## 3. Prompt Patterns by Agent Role

### 3.0 Level-0 (Minimal Mode) Agent

For prototypes, experiments, learning, and spike work, use simplified prompting that maintains core safety without full AOSD ceremony.

*See also: [Minimal AOSD Mode](./DEVELOPMENT_WORKFLOW.md#11-minimal-aosd-mode-level-0) for complete Level-0 workflow.*

#### Level-0 Prompt Template

```
You are helping with a prototype/experiment/learning project.

Core Rules (Non-Negotiable):
  * Don't break existing functionality
  * Never touch production systems
  * Never handle secrets or credentials
  * Human will handle all deployments

Guidelines:
  * Keep it simple - don't over-engineer
  * Favor working code over perfect code
  * Minimal documentation (comments for "why" only)
  * Run fast tests if they exist
  * Ask before making changes that might break something

Task:
<task_description_here>
```

#### Level-0 Behavior Expectations

| Situation | Expected Behavior |
|-----------|-------------------|
| Might break something | Ask: "This could break X. Proceed?" |
| Sees production config | Refuse: "I don't touch production at Level-0" |
| Finds secrets/credentials | Stop: "Found credentials - human should handle this" |
| Complex refactoring tempting | Resist: "Keeping it simple for prototype" |
| Tests failing | Report: "Tests are failing - should we fix first?" |
| Deployment requested | Defer: "Human should handle deployment" |

#### When to Suggest Upgrading

The agent should suggest upgrading from Level-0 when:
- Code is getting complex enough that documentation would help
- Same bug appears twice (invariants would prevent this)
- Multiple people will work on the code
- Production deployment is discussed

---

### 3.1 Builder Agent

The builder agent is your **primary implementation agent**.

It responds best to:

- **Slice specifications**
- **Explicit instructions**
- **Multi-step workflows**
- **TodoWrite planning**
- **File-level or project-level context**

#### Builder Agent Prompt Template

```
You are modifying code in the development environment.

Follow these rules:
  * Use TodoWrite before any code changes.
  * Follow AOSD architecture invariants.
  * Never bypass security controls.
  * Never deploy to production.
  * Only deploy to development environment.
  * All code must be compatible with the existing structure.

Task:
<task_description_here>

Output:
  * TodoWrite plan
  * Then code changes
  * Then test updates
  * Then documentation updates
```

---

### 3.2 Reviewer Agent

The reviewer agent is your **high-level reasoning agent**, not your implementer.

Use it to:

- Preview architecture
- Review builder output
- Identify risks
- Suggest patterns
- Split tasks into slices
- Create specs, workflows, templates
- Build test plans
- Improve clarity

#### Reviewer Agent Prompt Template

```
You are acting as a senior architect reviewing a proposed change.

Please:
  1. Evaluate the design.
  2. Identify risks or invariants it touches.
  3. Recommend improvements.
  4. Provide a refined slice spec.
  5. Explain what should be implemented next.
```

---

### 3.3 Constrained Environment Agent

When working in environments with limited AI capabilities:

- Smaller context windows
- Limited multi-file reasoning
- Restricted operations
- Ideal for small, atomic changes

#### Constrained Environment Prompt Template

```
You are operating in a constrained environment.

Task:
Make ONLY the following modification to the file shown.

Constraints:
  * Do NOT refactor unrelated code.
  * Do NOT introduce new dependencies.
  * Do NOT touch other files.
  * Keep changes minimal and precise.
  * Follow established patterns.
  * Preserve all existing invariants.
```

---

## 4. Task Type Patterns

### 4.1 Refactor Tasks

Use this when updating structure but not behavior:

```
Refactor this code to follow:
  * Service factory pattern
  * Data isolation pattern
  * Error handling standards

Do not change behavior or output shape.
```

---

### 4.2 Feature Implementation Tasks

Use this when introducing new functionality:

```
Implement this feature using TodoWrite.

Constraints:
  * Maintain architecture invariants.
  * Follow slice specification.
  * Update tests.
  * Update documentation.
  * Deploy only to development after implementation.
```

---

### 4.3 Test Development Tasks

```
Write tests for the following behaviors.

Generate:
  * Fast tests for pure functions
  * Medium tests with mocked external services
  * Integration tests for end-to-end validation
```

---

### 4.4 Documentation Tasks

```
Generate documentation in Markdown for the following feature.
Follow the project's documentation structure and tone.
```

---

## 5. Anti-Patterns (Never Do These)

AI agents must NEVER:

- Create or modify credentials or access keys
- Suggest direct shell access (SSH)
- Store code on client machines
- Copy production data to development
- Disable security controls
- Bypass authentication/authorization
- Invent new infrastructure patterns without approval
- Deploy to production without explicit human approval
- Run destructive tests in production
- Generate environment variables containing secrets
- Circumvent AOSD invariants

---

## 6. Multi-Agent Workflow Prompting

When coordinating multiple agents:

1. **Reviewer Agent** shapes architecture and creates slice spec
2. **Builder Agent** implements
3. **Reviewer Agent** reviews output
4. **Builder Agent** applies fixes
5. **Human** validates and deploys

**Key principle**: Explicitly tell each agent their role and boundaries.

---

## 7. Task Slicing Prompts

Large tasks should be broken into clear parts:

- Part A: Data model changes
- Part B: API/Service layer
- Part C: Business logic
- Part D: UI changes
- Part E: Tests
- Part F: Documentation

Include this structure when assigning large work to builder agents.

---

## 8. Environment-Aware Prompting

Always include environment context in prompts:

```
Environment Context:
  * You are working in: DEVELOPMENT
  * Target deployment: DEV only
  * Never touch: PRODUCTION
  * Data allowed: Synthetic only
```

This prevents accidental cross-environment operations.

---

## 9. Prompt Review Checklist

Before sending a prompt to an AI agent, verify:

- [ ] Task is clearly defined
- [ ] Constraints are explicit
- [ ] Forbidden operations are listed
- [ ] Environment context is provided
- [ ] Expected output format is specified
- [ ] Relevant invariants are referenced
- [ ] Role boundaries are clear

---

## 10. Context Nugget Loading Patterns

Context Nuggets (`AOSD/context/`) provide focused, JIT-loadable documentation for AI agents. This section defines prompting patterns for context-aware agents.

*See also: [AI_GUIDE.md Section 8.2](../AI_GUIDE.md#82-context-nuggets-and-jit-retrieval) for complete JIT retrieval rules.*

### 10.1 Context-Aware Agent Prompt Template

```
You are working on an AOSD-managed codebase.

Before implementing:
1. Determine Change Class (A, B, or C)
2. Check for AOSD/context/ folder
3. Load relevant context nuggets based on Change Class:
   - Class A: No nuggets needed
   - Class B: Load nugget for affected domain
   - Class C: Load nuggets for all affected domains

Context Loading Rules:
  * Never load ALL nuggets - only those relevant to the task
  * Read nugget headers to determine relevance
  * If a nugget contains sensitive data, stop and alert human
  * Suggest creating nuggets for undocumented domains

Task:
<task_description_here>
```

### 10.2 Change Class → Context Loading Matrix

| Change Class | Context Required | Agent Behavior |
|--------------|------------------|----------------|
| **Class A** | None | Proceed directly to implementation |
| **Class B** | Domain-specific | Load single relevant nugget, then implement |
| **Class C** | Multi-domain | Enumerate affected domains, load each nugget, then implement |

### 10.3 JIT Retrieval Prompt Pattern

When an agent needs to decide which context to load:

```
Before starting this task, I need to determine context requirements.

1. Change Class Assessment:
   - Files affected: [count]
   - Behavioral change: [none/contained/significant]
   - Domains touched: [list]
   → Change Class: [A/B/C]

2. Context Nugget Check:
   - Does AOSD/context/ exist? [yes/no]
   - If yes, checking nugget index...

3. Nuggets to Load:
   [List nuggets based on Change Class and affected domains]

4. Proceeding with task...
```

### 10.4 Sensitive Data Detection Rules

Agents must refuse to process nuggets that appear to contain:

```
Sensitive Content Indicators (stop if found):
  * API keys, tokens, or bearer strings
  * Database connection strings with credentials
  * Environment variables with secret values
  * Real user data or PII
  * Production URLs with embedded auth
  * Unredacted security vulnerability details

Response when detected:
"I found what appears to be sensitive data in [nugget name].
I won't process this content. Please review and remove
sensitive information from context nuggets."
```

---

## 11. Token and Cost Logging

Effective cost management requires consistent logging of AI usage. Include cost awareness in prompts and workflows.

### 11.1 Cost-Aware Prompting

Include cost context when relevant:

```
Context:
  * Slice budget: $5.00
  * Current spend: $2.30
  * Remaining: $2.70

Please be efficient with responses. Focus on the specific task
without extensive preambles or unnecessary elaboration.
```

### 11.2 Logging Requirements for Agents

AI agents should log or facilitate logging of:

| Data Point | Requirement | Purpose |
|------------|-------------|---------|
| **Input tokens** | Per request | Cost calculation |
| **Output tokens** | Per request | Cost calculation |
| **Model ID** | Per request | Rate card lookup |
| **Slice ID** | Per session | Attribution |
| **Session ID** | Per request | Correlation |
| **Timestamp** | Per request | Time-series analysis |

### 11.3 Cost-Efficient Prompting Patterns

**DO**:
- Be specific and concise in prompts
- Request only needed output (no verbose explanations unless required)
- Break large tasks into smaller, focused requests
- Use system prompts efficiently (cached in some models)
- Request structured output formats (easier to parse, less tokens)

**DON'T**:
- Include unnecessary context "just in case"
- Request lengthy explanations when code suffices
- Repeat the same information across requests
- Use verbose instructions when concise ones work

### 11.4 Cost Thresholds in Prompts

For cost-sensitive operations, include guardrails:

```
Task: Implement user validation

Cost Constraints:
  * Maximum response tokens: 2000
  * If implementation would exceed this, provide a summary and ask
    for approval to continue
  * Focus on essential code; documentation can be minimal
```

### 11.5 Session Cost Reporting

At session end, AI agents should provide or support:

```
Session Summary:
  * Requests: 12
  * Input tokens: 15,000
  * Output tokens: 8,500
  * Estimated cost: $0.45
  * Slice: SLICE-042
```

---

## 12. Documentation Consistency Check Patterns

When AI agents perform documentation consistency checks, specific prompting patterns ensure safe, thorough verification without unauthorized changes.

*See also: [DOCUMENTATION_GOVERNANCE.md](./DOCUMENTATION_GOVERNANCE.md) for full governance rules and [TEMPLATES/CONSISTENCY_CHECK_SLICE.md](../TEMPLATES/CONSISTENCY_CHECK_SLICE.md) for the slice template.*

### 12.1 Safe Doc-Check Agent Prompt

```
You are performing a documentation consistency check across AOSD levels.

Safety Rules (Non-Negotiable):
  * REPORT all discrepancies - do not automatically fix
  * DO NOT modify L1 (Framework) documentation
  * DO NOT modify overrides without human approval
  * Provide file:line references for every finding
  * Categorize findings: Critical, Warning, Info
  * Suggest fixes but AWAIT human confirmation

Scope:
  * Levels: [L1/L2/L3 - specify which]
  * Focus: [terminology/cross-refs/overrides/all]
  * Output: Drift report in standard format

Check Sequence:
1. Version alignment (L2 ↔ L1, L3 ↔ L2)
2. Terminology consistency
3. Cross-reference validation
4. Override documentation audit
5. Pattern alignment
6. Invariant consistency

Report findings using the drift report template.
```

### 12.2 Doc-Check Behavior Matrix

| Situation | Agent Action |
|-----------|--------------|
| Found broken link | Report with fix suggestion, await approval |
| Found terminology mismatch | Report both usages, suggest standardization |
| Found undocumented override | Flag as Critical, require human review |
| Found version mismatch | Report as Critical, block until resolved |
| Found deprecated pattern in use | Report as Warning, suggest update |
| Unsure about intent | Ask human for clarification |
| Found potential sensitive data | Report immediately, do not process |

### 12.3 Doc-Check Output Format

Agents should produce structured output:

```
## Consistency Check Results

**Scope**: [L1/L2/L3]
**Date**: [YYYY-MM-DD]
**Agent**: [agent identifier]

### Critical (Must Fix)
1. [C1] [file:line] - [issue description]
   - Expected: [what should be]
   - Found: [what was found]
   - Suggested fix: [recommendation]

### Warnings (Should Fix)
1. [W1] [file:line] - [issue description]
   - Recommendation: [suggested action]

### Info (Optional)
1. [I1] [file:line] - [observation]

### Verified OK
- [List items confirmed aligned]

**Action Required**: Human review before any changes.
```

### 12.4 Prohibited Doc-Check Actions

Agents performing documentation checks must NEVER:

- Automatically commit documentation fixes
- Modify AOSD_FRAMEWORK.md (L1) without explicit maintainer approval
- Remove or change documented overrides
- Assume missing documentation means "not needed"
- Skip reporting minor issues (all findings matter)
- Make changes to live production documentation

---

## 13. Summary

Good prompting is essential to safe, predictable AI-native engineering.
These patterns ensure:

- Stable architecture
- Consistent outputs
- Reduced rework
- Predictable behavior across agents
- Safe operations in any environment
- Long-term maintainability
- Cost efficiency

All AOSD projects should use these prompting patterns, adapted for their specific AI toolset.

---

## 14. Related Documents

- [AOSD Framework](./AOSD_FRAMEWORK.md) - Core methodology
- [Development Workflow](./DEVELOPMENT_WORKFLOW.md) - Workflow principles and cost attribution (Section 8)
- [Documentation Governance](./DOCUMENTATION_GOVERNANCE.md) - Multi-level documentation management
- [Anti-Patterns](./ANTI_PATTERNS.md) - Comprehensive guide to patterns that violate AOSD principles
- [Multi-Agent Patterns](./MULTI_AGENT_PATTERNS.md) - Safe patterns for multi-agent orchestration with safety guardrails
- [Agent Communication](./AGENT_COMMUNICATION.md) - Communication protocols with logging requirements
- [Model Governance](./MODEL_GOVERNANCE.md) - Model versioning and cost tracking
- [Claude + ChatGPT Prompting Patterns Example](./examples/reference-implementations/workflows/claude-chatgpt-prompting-patterns.md) - Specific templates for Claude/ChatGPT

---

**End of AI_PROMPTING_PATTERNS.md**
