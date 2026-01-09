# AOSD Course Overview
**A Training Curriculum for AI Orchestrated Systems Development**
**Designed for Jim Wiedman and engineers adopting AOSD**

---

## 1. Purpose of This Course

This curriculum teaches the full AOSD approach to **AOSD (AI Orchestrated Systems Development)**, incorporating:

- **The Six High-Level Goals** that drive all AOSD decisions
- Multi-agent workflows (ChatGPT, Claude Code, and other AI tools)
- AOSD architectural invariants
- Trustworthy-by-design principles (NIST SP 800-160)
- AI-first architecture patterns
- Security-first DevSecOps mindset
- Practical workflows across various deployment environments
- **Scaling AOSD** from solo developers to enterprise teams
- **Working in restrictive environments** (government, ITAR, healthcare, corporate)
- Terminal+vi development (Jim's workflow)
- Human-in-the-loop oversight
- AOSD's full testing strategy
- Slice-based development and TodoWrite planning
- Cross-environment validation with Playwright
- Release promotion and stable operations

This is the **personal mastery track** for you today, and **the onboarding track** for future engineers using AOSD.

---

## 2. Course Structure

The course consists of **eleven learning modules**, each focusing on a pillar of AOSD:

**Core Modules (1-7)**: Foundation through advanced multi-agent workflows
**Advanced Modules (8-11)**: Runtime AI, invariants mastery, framework evolution, and parallel development

### **Module 01 — AOSD Foundations**
You'll learn:
- **Why AOSD exists** (the six high-level goals)
- AOSD development philosophy
- HITL (Human-In-The-Loop) patterns
- AI-first architecture principles
- Trustworthy computing principles (NIST SP 800-160)
- Agent roles and boundaries
- Work Item Lifecycle (from request to completion)
- How AOSD differs from "classic" engineering

### **Module 02 — Architecture with AI**
You'll learn:
- How to describe architecture to AI agents
- How to enforce AOSD invariants
- How to design slices and features
- How to evaluate correctness, risk, and consistency
- How to get ChatGPT to architect and Claude to implement

### **Module 03 — Testing & TDD in AOSD**
You'll learn:
- Fast/Medium/Slow AOSD testing strategy
- Pure function extraction
- How AI writes and evolves tests
- How to keep Claude Code disciplined
- How Playwright fits into the test pyramid
- Using AI to diagnose bugs and test gaps

### **Module 04 — Environments & Operations**
You'll learn:
- Development environment workflows
- Production environment workflows
- Constrained environment adaptations
- SSM-only security posture
- Release promotion best practices
- How to structure multi-environment operations safely

### **Module 05 — Security & Compliance by Design**
You'll learn:
- NIST SP 800-160 trustworthy system design
- DevSecOps "shift left" mindset
- Secrets management
- WAF, IAM, logging, and multi-tenant isolation
- Designing systems for CMMC/FedRAMP/NIST SP 800-53
- AI prompt safety and data handling rules

### **Module 06 — Adapting AOSD to Your Environment**
You'll learn:
- **AOSD is deployment-agnostic** (SaaS, private cloud, on-prem, enclave)
- **Adaptation principles** for different AI capabilities
- **Types of deployment environments** (government, ITAR, corporate, healthcare, regulated)
- How slice size scales with model capability
- How human oversight scales inversely with AI reliability
- How invariants become more critical as capability decreases
- How methodology remains constant while tooling varies
- **Full content**: See [Adapting AOSD to Your Environment](../MANUAL/AOSD_FRAMEWORK.md#-adapting-aosd-to-your-environment) in the framework

### **Module 07 — Advanced Multi-Agent Workflows**
You'll learn:
- How to coordinate ChatGPT � Claude Code � Codex
- Conflict resolution between model opinions
- Multi-agent pipelines for complex features
- AOSD refactoring techniques
- How to evolve and govern AOSD systems over time

### **Module 08 — AI as a Runtime Component**
You'll learn:
- How runtime AI differs from development-time AI
- Seven new runtime AI invariants
- AWS Bedrock integration patterns
- AI Gateway architecture
- Testing AI features (golden traces, shadow mode, adversarial)
- Security and compliance for runtime AI
- Model monitoring and opportunity identification
- When NOT to use runtime AI
- How to propose, approve, and deploy AI features safely

### **Module 09 — Invariants Deep Dive**
You'll learn:
- How to design architectural invariants for your platform
- Why each invariant exists and what problems it solves
- How to apply invariants in practice
- How to recognize violations
- How invariants interact and reinforce each other
- How to enforce invariants through AI prompts
- When and how to evolve invariants safely

### **Module 10 — Framework Evolution & Meta-Governance**
You'll learn:
- How AOSD itself evolves over time
- Semantic versioning and CHANGELOG maintenance
- Compatibility declarations between framework levels
- When to challenge invariants (and when not to)
- How to propose framework improvements
- How to balance stability vs innovation
- How to contribute to living documentation
- Cross-project learning patterns
- Industry evolution integration
- Meta-governance principles

### **Module 11 — Teams, Scaling & Parallel Development**
You'll learn:
- **Scaling AOSD** from solo developer to enterprise teams
- Roles and responsibilities at each team size
- Slice distribution and branch ownership strategies
- AI-assisted coordination mechanisms
- When to use parallel development (safe vs unsafe scenarios)
- Session roles (PRIMARY, SECONDARY, MONITOR)
- Filesystem isolation with git worktrees
- Resource arbitration (deployment, tests, CPU)
- Merge strategy (SECONDARY after PRIMARY)
- Failure modes and recovery
- **Reference**: See [Scaling AOSD for Teams](../MANUAL/AOSD_FRAMEWORK.md) for comprehensive guidance
- **Note**: Relevant for all team sizes, from solo to enterprise

---

## 3. Learning Style & Philosophy

The syllabus is designed for:

- **hands-on** learning
- iterative practice
- real application improvements in your projects
- using your existing workflows (terminal+vi + Claude Code)
- building intuition through repeated slice cycles

The goal is mastery through repetition + pattern recognition + AOSD's stable guardrails.

### Note for AI Agents

If you are an AI agent helping a human learn AOSD, first read **[AI_GUIDE.md](../AI_GUIDE.md)** at the repository root. That guide explains:
- How to read AOSD docs in a token-efficient way
- Which sections to load for which kinds of tasks
- How to extract and apply AOSD principles and patterns
- How to guide humans through AOSD end-to-end

Use `AI_GUIDE.md` as your entrypoint before loading specific syllabus modules.

### Narrative Learning Resource

For a story-driven introduction to AOSD concepts, read **[The Orion Initiative: An AOSD Adoption Story](THE_ORION_INITIATIVE.md)**. This *Phoenix Project*-style narrative follows a team through eight months of adopting AOSD. It complements the technical modules by showing the human experience of transformation—the frustrations, breakthroughs, and gradual mindset shifts that come with adopting a new way of working with AI.

---

## 4. Recommended Sequencing

**Core Path (Modules 1-7)**:
1. Start with Module 01 to understand the worldview, mindset shift, and the six high-level goals.
2. Move to Module 02 to learn architecture formulation.
3. Use Module 03 during any coding slice to solidify testing patterns.
4. Use Module 04 whenever deploying to Dev/Staging/Prod.
5. Reference Module 05 on all compliance-oriented features.
6. Use Module 06 when adapting AOSD to different deployment environments (government, ITAR, healthcare, corporate, regulated).
7. Use Module 07 as you begin multi-agent workflows and advanced AOSD patterns.

**Advanced Path (Modules 8-11)**:
8. Complete Module 08 before building AOSD features (AI as runtime component).
9. Complete Module 09 for deep mastery of invariants (can be done anytime after Module 02).
10. Complete Module 10 to understand framework evolution and contribute improvements.
11. Complete Module 11 for team scaling and parallel development (relevant for all team sizes).

---

## 5. Intended Outcomes

Completing this course, you will:

- Understand **why AOSD exists** and articulate its six high-level goals
- Develop a deep intuition for AOSD coding
- Be able to orchestrate multi-agent development with confidence
- Build secure, trustworthy systems aligned with NIST 800-160
- Design AI-first architectures across AOSD projects platforms
- **Adapt AOSD to any environment**, including restrictive settings
- **Scale AOSD** from solo development to enterprise teams
- Enforce invariants without having to think about them
- Ship features faster, safer, and with more predictability
- Be able to onboard future developers with a shared standard

This syllabus is the foundation of your personal AOSD discipline — and the DNA of AOSD's engineering ecosystem.

---

**End of COURSE_OVERVIEW.md**
