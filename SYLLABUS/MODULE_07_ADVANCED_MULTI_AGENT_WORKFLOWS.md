# Module 07  Advanced Multi-Agent Workflows
**AOSD Curriculum**
**Coordinating Multiple AI Agents for Complex Development Tasks**

---

## 1. Purpose of This Module

This module teaches how to coordinate **multiple AI agents** to function as a unified engineering team:

- ChatGPT � architect / reviewer
- Claude Code � builder / implementer
- Codex � transformer / syntactic rewriter
- Other agents as needed for your deployment environment

You will learn:

- How to choose the right model for the right job
- How to hand off work between models safely
- How to avoid conflicts, pattern drift, and cross-agent confusion
- How to enforce AOSD invariants across all agents
- How to adapt workflows for different deployment environments
- How to structure long, multi-slice projects using multi-agent orchestration

This module completes the AOSD discipline.

---

# 2. Why Multi-Agent Workflows?

No single AI model does everything well.

| Model | Strengths | Weaknesses |
|-------|-----------|-------------|
| **Claude Code** | Multi-file reasoning, code generation, refactors | Not optimized for architectural risk analysis |
| **ChatGPT** | Architecture, design reasoning, risk identification | Not a safe code implementer |
| **Codex** | Deterministic rewriting, syntactic clarity | No architectural depth |
| **Constrained Models** | Minimal diffs, controlled changes | Cannot reason deeply, limited context |

Trying to use one model for all jobs creates drift and unpredictable behavior.
AOSD solves this through **division of labor**.

---

# 3. The AOSD Multi-Agent Pipeline

This is the **canonical multi-agent workflow**:

```
Human → ChatGPT → Claude Code → Human → (Optionally Codex) → Deploy → Playwright → Human → (Constrained env if needed)
```

Expanded:

### **Step 1  Human defines the feature**
Rough idea � hand to ChatGPT.

### **Step 2  ChatGPT creates slice spec**
ChatGPT produces:
- goals
- non-goals
- invariants
- risks
- test plan

Human approves.

### **Step 3  Claude Code produces TodoWrite**
Claude expands slice spec into:
- plan
- file-level tasks
- tests
- docs
- deployment instructions

Human approves.

### **Step 4  Claude Code implements**
- code
- tests
- docs
- runs fast/medium/slow
- deploys to Dev

### **Step 5  Human reviews**
Using the PR checklist.

### **Optional Step 6  Codex rewrites or formats isolated files**
If deterministic transforms needed, e.g.:
- type hinting
- converting sync � async
- normalizing function naming

Codex does **not** touch architecture.

### **Step 7  Dev deployment & Playwright**
Playwright validates flows.

### **Step 8  Human merges**
PR goes to main.

### **Step 9  Prod deploy (Prod EC2)**
Follow release checklist.

### **Step 10  Constrained Environments (if needed)**
Constrained environments receive small, safe, atomic changes only.

---

# 4. Roles and Hand-Off Rules

### **ChatGPT � Claude Code**
ChatGPT gives Claude:
- slice spec
- architectural notes
- invariants
- test expectations
- risk warnings

Claude must always:
- run TodoWrite
- follow invariants
- ask for confirmation

### **Claude Code � ChatGPT**
Claude sends:
- TodoWrite
- code diffs (if human pastes them)
- risks/questions

ChatGPT returns:
- architectural review
- suggestions
- risk mitigation
- structural corrections

### **Claude Code � Codex**
Use when you need:
- deterministic rewrites
- repetitive formatting fixes
- code transformations
- large search/replace operations

Claude performs surrounding reasoning;
Codex performs the mechanical transformation.

---

# 5. How to Decide Which Agent to Use

### **Claude Code?**
Use when:
- touching multiple files
- adding new handlers
- changing logic
- modifying tests
- updating prompt builders
- doing anything architectural but within code

### **ChatGPT?**
Use when:
- designing architecture
- structuring a slice
- doing a risk review
- evaluating invariants
- evaluating ideas
- writing documentation

### **Codex?**
Use when:
- rewriting code in place
- pure mechanical transformations
- updating code style

### **Constrained Environment Agents?**
Use when:
- the change is *tiny*
- one file
- one small diff
- low risk
- working in a constrained deployment environment

Never use for architecture, refactors, or invariants. See [Adapting AOSD to Your Environment](../MANUAL/AOSD_FRAMEWORK.md#-adapting-aosd-to-your-environment) for guidance.

---

# 6. Managing Long-Running Features or Refactors

For multi-slice features:

1. ChatGPT produces full **feature spec**
2. Human + ChatGPT break into slices
3. Claude implements slice by slice
4. Human reviews each slice
5. Invariants applied in every slice
6. Codex may perform repetitive rewrites
7. Constrained environments handle only atomic, low-risk changes

### Important:
Large refactors should always be done in your primary development environment, not constrained environments.

---

# 7. Avoiding Multi-Agent Failure Modes

Common traps:

### **7.1 Duplicate Patterns**
AI rewriting code into a new structure.
Solution: enforce invariants + specs + TodoWrite.

### **7.2 Architectural Drift**
Small changes accumulate into inconsistency.
Solution: use ChatGPT reviews + MEMORY cards.

### **7.3 Agent Confusion**
Asking the wrong agent to do the wrong job.
Solution: follow the role boundaries strictly.

### **7.4 Constrained Environment Overreach**
Trying to do too much with limited AI capabilities.
Solution: enforce atomicity always.

---

# 8. Advanced Techniques

### **8.1 Contrastive AI Architecture Review**
Have ChatGPT and Claude Code disagree on purpose:
> "ChatGPT, critique Claude's TodoWrite plan."

### **8.2 Multi-Agent Synchronization**
Example:
- ChatGPT � writes architectural rule
- Claude � updates implementation
- ChatGPT � validates invariants
- Codex � rewrites a module
- Constrained agent → fix edge case in constrained environment

### **8.3 AI-First Refactor Planning**
Use ChatGPT to create a **refactor plan**, then give only the safe portions to Claude.

### **8.4 Multi-Agent Codebase QA**
Run a multi-agent audit:
1. ChatGPT � find architectural risks
2. Claude � scan for inconsistent patterns
3. Codex � rewrite or normalize
4. Human � review

---

# 9. Exercises

### Exercise 1  Three-Agent Slice
Pick a feature and map:
- ChatGPT tasks
- Claude tasks
- Codex tasks

### Exercise 2  Environment Split
Take a real bug and split into:
- Primary development environment work
- Constrained environment micro-fix

### Exercise 3  Invariant Enforcement Drill
Use ChatGPT to list invariants violated by a given code snippet (paste one in).

### Exercise 4  Multi-Agent Code Review
Paste a diff and ask:
- "ChatGPT, review this architecturally."
Then:
- "Claude, apply these corrections via TodoWrite."

---

# 10. Completion Criteria

You've mastered this module when you:

- Can route tasks to the correct AI agent automatically
- Can slice features for multi-agent execution
- Understand how to avoid drift and maintain safety
- Can orchestrate ChatGPT � Claude � Codex hand-offs
- Know how to incorporate constrained environment micro-changes safely
- See how invariants keep multi-agent work predictable
- Can reliably run full multi-agent pipelines on real features

This completes the Core AOSD Syllabus.

---

**End of MODULE_07_ADVANCED_MULTI_AGENT_WORKFLOWS.md**
