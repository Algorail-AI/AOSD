# Changelog

All notable changes to the AOSD Framework will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

*No unreleased changes.*

---

## [1.0.0] - 2026-01-08

**Initial public release of the AOSD Framework.**

AOSD (AI-Orchestrated Systems Development) is a methodology for orchestrating AI agents to build and maintain production-grade software systems. This release represents a stable, coherent framework refined through real-world usage.

### Core Framework

- **11 Core Principles** covering multi-agent orchestration, trustworthy-by-design, environment isolation, architectural invariants, slice-based development, TodoWrite workflow, testing strategy, human-in-the-loop oversight, functional core/imperative shell, version control, and work item lifecycle

- **9 Architecture Patterns** including Environment Factory, API Security Layer, Multi-Tenant Data Isolation, Deployment Bundling, Decision Catalog, Revision Instructions, Agent Context Memory Files, IaC Invariant Enforcement, and AI Evaluation/Continuous Validation

- **Three-Level Architecture**: Level 1 (Framework) → Level 2 (Orchestration Profile) → Level 3 (Application-Specific)

- **Adoption Maturity Model** with four levels (0-3) from minimal mode through advanced

### Operational Infrastructure

- Multi-agent orchestration patterns and safety guardrails
- Session management and handoff protocols
- Cost attribution and token tracking
- Change Classes (A/B/C) with calibrated oversight
- Invariants Cookbook with executable fitness functions
- Context engineering with JIT retrieval patterns

### Documentation

- AI_GUIDE.md for AI agent methodology guidance
- AGENT.md for tool-agnostic operator contracts
- META_GUIDE.md for framework self-maintenance
- Comprehensive template library
- Training curriculum (SYLLABUS/)

### Governance

- Strategic decisions documented in DECISIONS.md
- Evolution protocol via invariant pressure assessment
- Extension/overlay structure for adopting organizations

---

## Versioning Policy

### What Constitutes a Breaking Change (Major Version)
- Removal or renaming of core principles
- Fundamental changes to the three-level architecture
- Changes that would invalidate existing Level 2 Orchestration Profiles
- Removal of required template sections

### What Constitutes a Feature (Minor Version)
- New principles added
- New patterns added
- New templates added
- New training modules
- Enhancements to existing guidance

### What Constitutes a Patch
- Typo corrections
- Clarifications that don't change meaning
- Example updates
- Documentation improvements

### Compatibility Declaration
Orchestration Profiles (Level 2) should declare which framework version they target:
```markdown
## Framework Compatibility
**AOSD Framework Version**: 1.x
**Tested With**: 1.0.0
```

[Unreleased]: https://github.com/Algorail-AI/AOSD/compare/v1.0.0...HEAD
[1.0.0]: https://github.com/Algorail-AI/AOSD/releases/tag/v1.0.0
