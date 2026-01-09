# Module 01  AOSD Foundations
**AOSD Curriculum**
**Designed for Jim Wiedman and engineers adopting AOSD**

---

## 1. Purpose of This Module

This module establishes the core mental model for AOSD using AOSD.

You will learn:

- **Why AOSD exists** and its six high-level goals
- How AOSD differs from traditional software engineering
- How to work with multiple AI agents in distinct roles
- Why AOSD's invariants exist and how they fit into trustworthy design
- What "AI-first architecture" means beyond adding AI onto existing systems
- How NIST SP 800-160 "Trustworthy System Design" principles integrate into your workflow
- Why human-in-the-loop control is essential
- How your environment (development, staging, production) shapes everything
- Why predictable patterns matter for multi-agent collaboration

This module is about **mindset**, not mechanics.

### Recommended Reading

Before diving into the concepts below, consider reading **[The Orion Initiative: An AOSD Adoption Story](THE_ORION_INITIATIVE.md)**. This *Phoenix Project*-style narrative follows a team through eight months of adopting AOSD, showing the human experience of transformation. It illustrates many of the concepts in this module through story rather than explanation.

---

# 2. Why AOSD Exists: The Six High-Level Goals

Before understanding *how* AOSD works, you must understand *why* it exists. AOSD is built to achieve six high-level goals:

### Goal 1 — Build Sustainable Systems That Outlive Their Original Developers

AI accelerates development, but without structure, systems become tied to the person who built them. AOSD prevents **human-dependency debt** through predictable architecture, consistent patterns, AI-maintained documentation, and full traceability.

### Goal 2 — Engineer Security and Trustworthiness from the Start

Security must be designed in—not added later. AOSD applies principles inspired by NIST SP 800-160 to make security a continuous engineering practice.

### Goal 3 — Accelerate Development and Keep Evolution Fast, Safe, and Low-Cost

AI can generate code quickly, but sustainable velocity requires structure. AOSD uses slices, invariants, trust zones, and clear workflows to ensure both initial development and future changes remain fast and predictable.

### Goal 4 — Minimize Operational Cost Through Lean, Cloud-Native Architecture

AI can generate complex systems, but complexity increases cost. AOSD emphasizes serverless-first patterns, pay-per-use execution, and minimal dependencies.

### Goal 5 — Integrate AI Into Applications as First-Class Runtime Capabilities

AI shouldn't only help build the application; it should also enhance it. AOSD supports adding intelligent features directly into applications.

### Goal 6 — Embed Training and Knowledge Directly Into Applications

AI enables applications to train their users directly through inline explanations, integrated help, and AI-assisted onboarding.

**Reference**: See the [Why AOSD Exists](../MANUAL/AOSD_FRAMEWORK.md) section in the framework for detailed explanations of each goal.

---

# 3. AOSD: A Different Discipline

Traditional software engineering optimizes for:

- minimizing change
- avoiding refactors
- predictable human labor
- local development
- rigid CI/CD
- carefully hand-tuned architecture

AOSD optimizes for:

- **rapid iteration**
- **fast, reversible changes**
- **AI-generated architecture & code**
- **refactoring as a cheap operation**
- **global reasoning across codebases**
- **multi-agent collaboration**

AOSD is not simply "using AI for autocomplete."
It is a **shift in mental model**.

---

# 4. AI-First Architecture: Designing Systems With AI at the Core

AI-first design means:

- Architecting systems **so they can be efficiently maintained by AI**
- Using patterns that AI can reliably reproduce and reason about
- Writing documentation and contracts the AI can parse
- Breaking problems into slices that map naturally to model strengths
- Ensuring invariants are clear, explicit, and enforceable

AI-first design is not:

- Adding a chatbot
- Adding small AI helpers
- Letting AI randomly code wherever it wants

AI-first design requires:

- predictable structure
- stable patterns
- minimal abstractions
- clear naming
- consistent file organization
- consistent data models
- minimal cross-file ambiguity

AI needs **stable terrain** to operate effectively.

---

# 5. Trustworthy Computing (NIST SP 800-160) + AOSD

NIST SP 800-160 principles map **beautifully** onto AOSD.

Key 800-160 themes incorporated in AOSD workflows:

### **4.1 Design for Trustworthiness**
- AOSD's invariants ensure systems are *inherently* trustworthy
- AWS Factory Pattern prevents accidental misconfiguration
- Multi-tenant DDB prevents cross-tenant leakage
- WAF Pattern ensures protection at every API boundary

### **4.2 Architect for Resilience**
AOSD systems change rapidly  resilience is built through:

- reversible slices
- clean versioning
- diffable outputs
- automated testing
- stateless Lambdas
- durable storage patterns
- stable PK/SK semantics

### **4.3 Human Oversight (HITL)**
NIST emphasizes that human review is part of trustworthy design.

AOSD embodies HITL through:

- TodoWrite approval
- PR reviews
- ChatGPT architectural review
- Prod deployments only on Prod EC2
- Explicit confirmation for risky operations

### **4.4 Security is a Design Input, Not an Output**
Security is integrated from the start:

- WAF
- IAM least privilege
- secure environment patterns
- SSM-only access
- deterministic logging
- structured decision catalogs
- cross-key invariants

AOSD + NIST 800-160 = secure, resilient, trustworthy automation.

---

# 6. Multi-Agent Collaboration Philosophy

AOSD using AOSD uses distinct specialized agents:

- **ChatGPT = Architect / Reviewer**
- **Claude Code = Builder / Implementer**
- **Codex = Structured Rewriter / Transformer**
- **Constrained AI = Constrained Environment Dev**

Why?

Different models have different strengths:

- Claude = multi-file reasoning
- ChatGPT = big-picture clarity
- Planning tools for task decomposition
- Execution tools for minimal diffs
- Sonnet = safe in restricted environments

Trying to force one model to do everything causes:

- drift
- errors
- conflicts
- hallucinations
- fragile code

Multi-agent specialization increases stability and predictability.

---

# 7. Work Item Lifecycle (Principle 11)

AOSD formalizes how work flows from request to completion. This is **Principle 11: Work Item Lifecycle**.

### 7.1 The Six Stages

Every piece of work follows this lifecycle:

```
┌─────────────┐   ┌──────────────┐   ┌─────────────────┐
│  Creation   │ → │   Analysis   │ → │ Branch Creation │
└─────────────┘   └──────────────┘   └─────────────────┘
                                              │
┌─────────────┐   ┌──────────────┐   ┌───────▼─────────┐
│   Merge &   │ ← │    Review    │ ← │ Implementation  │
│   Closure   │   │              │   │                 │
└─────────────┘   └──────────────┘   └─────────────────┘
```

| Stage | What Happens | Who Does It |
|-------|--------------|-------------|
| **Creation** | Work item created with clear description and acceptance criteria | Human or AI |
| **Analysis** | Requirements clarified, approach determined | Human + AI (ChatGPT) |
| **Branch Creation** | Isolated workspace created from work item | AI agent |
| **Implementation** | Code written, tests added, slice completed | AI agent (Claude) |
| **Review** | Code reviewed, tests verified, changes validated | Human + AI (Reviewer) |
| **Merge & Closure** | Code merged to main, work item closed | Human approval |

### 7.2 Why This Matters

**Traceability**: Every change links back to a work item
**Accountability**: Clear ownership at each stage
**Predictability**: AI agents know what to do at each stage
**Quality Gates**: Human review before merge ensures oversight

### 7.3 Work Item Template

Every work item should include:

```markdown
## Description
[What needs to be done and why]

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Technical Notes
[Relevant technical context]

## Related
- Slice spec: [link if applicable]
- Depends on: #[number] if applicable
```

### 7.4 Branch Naming Convention

Branches link to work items:
```
{type}/{issue-number}-{short-description}

Examples:
- feature/42-add-user-authentication
- fix/57-login-button-error
- refactor/89-optimize-queries
```

### 7.5 Commit Message Format

Commits reference work items:
```
{type}: {description} (#{issue-number})

Examples:
- feat: add login endpoint (#42)
- fix: resolve null pointer (#57)
- refactor: extract validation (#89)
```

**Magic keywords** auto-close issues when PR merges:
- `Fixes #42`
- `Closes #42`
- `Resolves #42`

Work Item Lifecycle ensures that AI agents operate within a structured, traceable workflow.

---

# 8. Human-in-the-Loop (HITL)

AOSD workflows *still rely on humans*.
HITL is essential because:

- AI can't reliably handle business logic ambiguity
- Architectural decisions require judgment
- Compliance and security require human validation
- Model drift occurs without guardrails
- Prod deployments require risk analysis

HITL in AOSD is formalized through:

- TodoWrite approval
- PR review checklist
- Slice spec review
- ChatGPT pre-implementation validation
- Prod EC2-only deploys
- Manual UAT in Dev
- Post-release validation

AOSD does *not* support fully autonomous AOSD engineering without HITL.

---

# 9. Risk-Based TodoWrite Tiers

Not all changes carry equal risk. AOSD Principle 6 (TodoWrite Workflow) uses tiered risk levels to calibrate planning effort and human oversight.

### The Four Risk Tiers

| Tier | Risk Level | Examples | HITL Requirement | TodoWrite |
|------|------------|----------|------------------|-----------|
| **High** | Security, data, production | IAM policies, DB migrations, auth changes, prod config | Full review required | Mandatory |
| **Medium** | Business logic, integrations | API endpoints, service calls, handlers | Spot check | Recommended |
| **Low** | UI, tests, documentation | Component styling, unit tests, comments | Automated only | Optional |
| **Zero** | Generated, trivial | Boilerplate, imports, formatting, typos | None | Skip |

### Risk Assessment Criteria

When determining the risk tier for a change, ask:

- **Does it touch invariants or security boundaries?** → High
- **Is it a multi-file or architectural change?** → High
- **Does it modify code logic, handlers, or business rules?** → Medium
- **Is it documentation or comments only?** → Low
- **Is it formatting or trivial fixes?** → Zero

### Why Tiers Matter

Risk tiers operationalize slice planning by:

- **Preventing over-engineering**: Trivial changes don't need full TodoWrite plans
- **Ensuring oversight**: Critical changes get proper human review
- **Creating shared mental models**: AI and humans agree on what's risky
- **Supporting security-by-design**: High-risk changes are flagged automatically
- **Reducing friction**: Low-risk work proceeds quickly

### Practical Application

Before starting any implementation work:

1. **Assess the risk tier** based on the criteria above
2. **Follow the appropriate workflow**:
   - High: Full TodoWrite, human approval before execution
   - Medium: TodoWrite recommended, spot-check review
   - Low: Optional TodoWrite, proceed with awareness
   - Zero: Skip TodoWrite, just fix it

This ensures effort matches risk — you don't spend 30 minutes planning a typo fix, but you don't skip planning for a database migration either.

---

# 10. DevSecOps & "Shift Left" Security

You integrate security at every stage:

- AI prompting includes security checks
- TodoWrite requires security consideration
- Dev workflow includes `make security-pre-deploy`
- WAF is mandatory
- MFA + SSM-only access
- Multi-tenant constraints baked into every handler
- IAM scoped
- No local secrets
- Constrained environment rules observed

Security is not bolted-on:
**It is an architectural invariant.**

---

# 11. Jim's Environment Model (Key to the Curriculum)

### **Commercial (Main Engineering Environment)**
- **Development Environment** - all coding, testing, deploying-to-dev
- Editor: **vi/vim**
- AI: **Claude Code** in the terminal
- ChatGPT as reviewer/architect (outside EC2)
- Prod deploys only from **Prod EC2**

### **Constrained Environments**
- Restricted access machines
- Editor: **VS Code** or similar
- AI: **Constrained models with limited context**
- Only micro-edits, single-file patches
- No heavy architecture work

### **Laptop**
- Playwright test runner
- Thin client  no canonical code

This module teaches how these constraints shape your AOSD thinking.

---

# 12. What "AOSD" Actually Means for You

By the time this module is complete, you should internalize:

- AI is a collaborator, not a code monkey
- Clear specs � better AI output
- Invariants = the spine of AOSD
- Good prompting ` more words; it means **structured expectations**
- AOSD codebases must be predictable, stable, and testable
- Rewrites are cheap (in development environments), so fear of refactor vanishes
- Design for collaboration between multiple AIs
- Human oversight is part of system safety
- AI-first architecture produces long-term leverage

AOSD development is less about "writing code faster" and more about **designing systems that AI can safely evolve with you**.

---

# 13. Exercises

### Exercise 1  Identify Invariants
Pick a recent change you made in your project.
Identify which invariants applied:
- AWS Factory
- WAF
- MT DDB
- Bundling
- Decision System
- Revision Instructions
- SSM-only

### Exercise 2  Rewrite a Problem Statement as a Slice Spec
Choose a feature and rewrite it using the slice spec template.

### Exercise 3  Analyze Your Last Few Prompt Interactions
Identify:
- Where Claude struggled
- Where ChatGPT added clarity
- Where invariants improved consistency

### Exercise 4  Architecture vs Implementation
Take a planned feature and split it into:
- ChatGPT architecture tasks
- Claude Code implementation tasks

### Exercise 5  Work Item Lifecycle
Create a work item for a real or hypothetical feature:
1. Write the description and acceptance criteria
2. Determine what branch name you would use
3. Write sample commit messages that reference the work item
4. Identify which lifecycle stages require human review

### Exercise 6  Risk Tier Assessment
For each of the following changes, determine the appropriate risk tier and justify your choice:
1. Fixing a typo in a README file
2. Adding a new API endpoint that stores user data
3. Modifying IAM policy permissions
4. Adding a CSS animation to a button
5. Refactoring a business logic handler into smaller functions

### Exercise 7  The Orion Initiative Discussion
Read the [Orion Initiative narrative](THE_ORION_INITIATIVE.md) and discuss:
1. What was Alex's team doing wrong before AOSD?
2. Why did Jordan's approach fail initially, and what changed?
3. How did the team handle Marcus's architectural drift?
4. What made the Month 6 incident different from the opening incident?
5. Which moment in the story most resonates with your own experience?

---

# 14. Completion Criteria

You've mastered this module when you:

- Can articulate **why AOSD exists** and explain the six high-level goals
- Can describe the AOSD mindset
- Understand why AOSD uses strong invariants
- Can articulate NIST 800-160 principles as applied to AOSD work
- Can determine when a feature is "builder agent work" vs "reviewer agent work"
- Understand why constrained and restrictive environments require tiny changes only
- Can reliably use specs, slice specs, and TodoWrite
- Can assess risk tiers and apply appropriate planning effort
- Understand the Work Item Lifecycle and can apply it to track work

When these concepts feel natural, move to **Module 02  Architecture with AI**.

---

**End of MODULE_01_FOUNDATIONS.md**
