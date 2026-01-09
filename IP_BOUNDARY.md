# IP Boundary

**What's Public vs. Private in the AOSD Ecosystem**

---

## Overview

AOSD uses a three-level architecture that naturally defines the boundary between public and private intellectual property. This document clarifies what belongs where.

---

## Level 1: AOSD Framework (Public)

**This repository** — universal, environment-agnostic methodology.

### What's Included
- Core AOSD principles and patterns
- Templates for adoption (slice specs, ADRs, app templates)
- Reference implementations (illustrative examples)
- Training curriculum (SYLLABUS)
- Methodology documentation

### Why It's Public
- Establishes AOSD as a shared vocabulary
- Enables adoption and community growth
- Provides value through education, not secrecy
- The methodology itself is not the competitive advantage

---

## Level 2: Orchestration Profile (Private)

**Separate repository** — organization-specific implementation choices.

### What's Included
- AI agent configurations and wiring
- Prompt templates and orchestration logic
- Delivery playbooks and engagement patterns
- Organization-specific invariants
- Operational lessons learned
- Internal tooling and automation

### Why It's Private
- Contains operational know-how developed through experience
- Represents competitive advantage in execution
- May include proprietary patterns and techniques
- Specific to how Algorail AI applies AOSD

---

## Level 2 Reference: Example Tooling (Public)

**Separate repository** — example implementation to lower adoption friction.

### What's Included
- CLI scaffolding tools (`aosd init`, `aosd slice new`, etc.)
- Linting and validation utilities
- Evidence package assembly
- GitHub Actions examples

### Why It's Public
- Helps others adopt AOSD (grows the ecosystem)
- Contains "boring" scaffolding, not operational secrets
- Explicitly positioned as "one way, not the only way"
- Encourages standardization around AOSD patterns

---

## Level 3: Application-Specific (Private)

**In each application repository** — project-specific documentation.

### What's Included
- Application-specific invariants
- Project architecture decisions (ADRs)
- Slice specifications for that project
- Context nuggets and critical file documentation

### Why It's Private
- Contains client/project-specific details
- Lives in application repositories (not AOSD framework)
- Naturally private as part of project codebases

---

## Summary Table

| Level | Repository | Visibility | Contains |
|-------|------------|------------|----------|
| Level 1 | AOSD | **Public** | Methodology, principles, templates, training |
| Level 2 | algorail-orchestration | **Private** | Agent configs, prompts, playbooks |
| Level 2 Ref | aosd-reference | **Public** | Example CLI tooling, scaffolding |
| Level 3 | (app repos) | **Private** | Project-specific AOSD documentation |

---

## Guiding Principle

> **Publish the "what" and "why" freely. Keep the "how we do it operationally" private.**

The framework teaches AOSD. The orchestration profile implements it. The distinction is clear:

- **Framework** = education, vocabulary, patterns → share broadly
- **Orchestration** = operational execution, accumulated expertise → competitive advantage

---

## Trademark Notice

AOSD™ is a framework stewarded by Algorail AI, LLC.
