# Module 02  Architecture with AI
**AOSD Curriculum**
**Designing Secure, Trustworthy, AI-First Architectures**

---

## 1. Purpose of This Module

This module teaches how to design architecture **with AI**, not just "before using AI."

You will learn:

- How to collaborate with AI tools (ChatGPT, Claude Code) to design systems
- How to enforce AOSD architectural invariants during design
- How to translate goals into slice specs
- How to validate architecture with NIST SP 800-160 "trustworthy design" principles
- How to build systems that AI can safely evolve
- How to avoid architectural drift
- How to use multi-agent collaboration to refine designs
- How to evaluate risk, correctness, and maintainability using AI

This is the **architecture brain** of your engineering system.

---

# 2. AOSD Architecture Concepts

Building software with AI requires a different architectural mindset.

### **2.1 Architecture Must Be Predictable**
AI thrives with:
- consistent naming
- consistent directory layout
- stable PK/SK patterns
- stable invariants

AI struggles with:
- clever abstractions
- dynamically inferred behavior
- inconsistent patterns
- "surprise" patterns

Your architecture must be optimized for **machine interpretability** as much as human readability.

---

### **2.2 Architecture Must Be Declarative**
AI tools perform best when the architecture is:

- explained clearly
- described in structured language
- encoded in MEMORY pattern cards
- consistent across slices

Your job: give the AI a stable conceptual map.

---

### **2.3 Architecture Must Be Modular & Slice-Friendly**
All work must be sliceable:

- No feature should require multi-week up-front architecture
- No slice should affect large swaths of the system
- No slice should change invariants unless explicitly approved
- Each slice should be internally coherent and low-risk

Slicing is the *unit of progress* for AOSD.

---

### **2.4 Architecture Must Embed Security from the Start (NIST SP 800-160)**
The architecture must be trustworthy by construction:

- multi-tenant isolation
- WAF protection
- trusted AWS factory interfaces
- deterministic bundling
- explicit state transitions
- versioned decisions
- auditability
- HITL approval cycles

Security is not a bolt-on  it is an invariant.

---

# 3. How to Use ChatGPT for Architecture

ChatGPT is the **architect and reviewer**, not the implementer.

### **3.1 What to Ask ChatGPT**
Ask ChatGPT to:

- Generate slice specs
- Validate architectural choices
- Rewrite requirements into structured tasks
- Identify risks, edge cases, and design flaws
- Evaluate differences between alternative approaches
- Map design to AOSD invariants
- Point out unclear requirements
- Determine safe order of operations
- Create the TodoWrite seed list

### **3.2 What NOT to Ask ChatGPT**
ChatGPT should not:

- Write full multi-file implementations
- Generate giant diffs
- Perform refactors
- Design new AWS patterns
- Modify invariants without explicit approval

That's Claude Code's job (with TodoWrite as the boundary).

---

# 4. How to Use Claude Code for Architecture

Claude Code is the **implementer**, but you can still use it for *light architectural tasks*:

Claude Code is appropriate for:

- suggesting data model adjustments (validated by ChatGPT)
- mapping slice specs to affected modules
- identifying missing components
- reasoning through code-level impacts
- preparing TodoWrite plans that touch architecture

But Claude Code must not make final decisions without:

- slice spec
- ChatGPT review
- human confirmation

---

# 5. Designing Slice Specifications

This section teaches you how to turn "ideas" into implementable slices using the **Slice Spec Template**.

### **5.1 Characteristics of a Good Slice**
- solvable in d 12 work sessions
- internally coherent
- avoids architectural drift
- references AOSD invariants
- produces at least one user-visible improvement
- complete enough for Claude Code to implement

### **5.2 When to Break Down a Slice**
Break into smaller slices if:

- it spans multiple Lambdas
- it requires table migrations
- it affects prompt-building logic
- it modifies multiple invariants
- it touches security boundaries
- it requires changes across multiple repos
- it cannot be implemented in a TodoWrite plan without risk

---

# 6. Evaluating Architecture with AOSD Invariants

Whenever ChatGPT or the human architect evaluates a slice, they must review:

### **AWS Factory Pattern**
- no raw boto3
- correct client reuse
- safe retry strategy

### **Lambda Bundling Pattern**
- correct `/asset-output` structure
- all packaging steps intact

### **Multi-Tenant DynamoDB Pattern**
- PK/SK patterns unchanged
- client validation enforced
- no cross-tenant access

### **WAF Pattern**
- every API must be protected
- rate limiting preserved
- no regressions

### **Decision System Pattern**
- catalog integrity preserved
- precedence logic enforced

### **Revision Instructions Pattern**
- prompt builders maintain injection points

### **SSM-Only Access Pattern**
- no SSH
- no local codebase injection

### **ODV/Future Framework Parameters Pattern**
- hidden infra preserved
- no unauthorized activation

### **NIST SP 800-160 Trustworthy Design**
- cybersecurity principles implemented
- clear HITL checkpoints
- assurance arguments internally consistent

Architecture review = invariants + trustworthy design + slice spec alignment.

---

# 7. Architecture Risk & Safety Evaluation

ChatGPT should evaluate slices for:

### **7.1 Correctness Risk**
- Does the slice break invariants?
- Does it create inconsistent state?
- Does it introduce ambiguity?

### **7.2 Security Risk**
- Does it affect access boundaries?
- Does it expose sensitive data?
- Does it change IAM, WAF, or DDB?

### **7.3 Maintainability Risk**
- Is the change too complex?
- Will AI be able to maintain it?
- Is the pattern stable for future slices?

### **7.4 Architectural Drift Risk**
- Does this slice attempt to create a new pattern?
- Does it subtly change data models?
- Does it conflict with existing invariants?

---

# 8. AI-First Architecture Principles (AOSD)

When designing systems that AI must maintain over time, follow these rules:

### **8.1 Prefer Explicitness Over Cleverness**
AI does not thrive on clever abstractions.

### **8.2 Prefer Flat Over Deep**
Too many layers confuse model reasoning.

### **8.3 Prefer Small Modules Over Large Ones**
AI handles small, single-purpose modules well.

### **8.4 Prefer Unambiguous Naming**
Names must be descriptive and predictable.

### **8.5 Prefer One Way to Do Things**
Too many patterns � ambiguous � AI mistakes.

### **8.6 Prefer Deterministic Data Models**
Consistent PK/SK patterns keep AI grounded.

### **8.7 Prefer Reversible Changes**
Slices must be easy to undo.

### **8.8 Prefer Stable Prompt Builders**
Focus on structure and invariants vs improvisation.

### **8.9 Prefer Predictable Infrastructure**
Deployment patterns must remain consistent.

### **8.10 Prefer HITL Validation**
Humans validate architecture  not the model.

---

# 9. Exercises

### **Exercise 1  Build a Slice Spec**
Take a small feature from your project and build a slice spec using the template.

### **Exercise 2  Architecture Review**
Paste a Claude Code TodoWrite plan into ChatGPT and annotate:
- risks
- invariants touched
- missing steps
- missing tests

### **Exercise 3  Invariant Trace**
Pick one of your existing Lambdas and trace which invariants apply.

### **Exercise 4  Ask ChatGPT to Evaluate Two Designs**
Give ChatGPT:
- design A (incorrect or risky)
- design B (cleaner)

Ask it to:
- evaluate both
- choose the safer one
- explain why

---

# 10. Completion Criteria

You've mastered this module when you can:

- Consistently produce crystal-clear slice specs
- Spot architecture drift instantly
- Enforce invariants "by feel," not by checklist
- Use ChatGPT as an architectural reviewer reliably
- Break large features into safe slices
- Design AI-first, trustworthy-by-design systems
- Confidently slice work for development vs constrained environments
- Reason about architecture with multiple AI agents

Once comfortable, move on to **Module 03  Testing & TDD in AOSD Development**.

---

**End of MODULE_02_ARCHITECTURE_WITH_AI.md**
