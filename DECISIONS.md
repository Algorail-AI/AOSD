# AOSD Strategic Decisions

**This document records *decisions*, not proposals.**
If you want to revisit a decision, open an issue with rationale.

---

## Governance Principle

Framework decisions are driven by **invariant pressure and observed failure**, not external novelty. Efficiency gains and tooling improvements are addressed at the profile level (Level 2) unless they challenge core principles.

For the full evolution protocol, see [META_GUIDE.md — Invariant Pressure Assessment](MANUAL/META_GUIDE.md#invariant-pressure-assessment).

---

This document captures resolved strategic decisions for AOSD. These are framework-level design decisions made during AOSD's evolution, not Architecture Decision Records (ADRs), which are for application-level architectural choices.

## How to Read This Document

Each decision includes:
- **Status**: Current state (Decided, Superseded, Under Review)
- **Date**: When the decision was finalized (YYYY-MM-DD)
- **Decision**: The actual choice made
- **Rationale**: Why this choice was made
- **Implications**: What this means for the framework and adopters
- **Revisit Trigger**: Conditions under which to reconsider (if applicable)

## How to Propose a New Decision

1. Open a GitHub issue describing the decision needed
2. Include alternatives considered and tradeoffs
3. Reference any related prior decisions
4. If accepted, a maintainer will add to this document

---

## Decisions

### D1: Framework vs. Standard

**Status**: Decided
**Date**: 2026-01-08

**Decision**: AOSD remains a framework, not a standard.

**Rationale**:
- Frameworks guide; standards mandate. AOSD aims to help adopters succeed, not certify compliance.
- The methodology is still evolving based on real-world usage.
- Conformance testing infrastructure would divert resources from core methodology development.
- Organic adoption builds understanding; premature standardization can create cargo-culting.

**Implications**:
- No conformance requirements or certification language at Level 1
- No MUST/SHOULD enforcement language (descriptive, not prescriptive)
- Adopters adapt patterns to their context rather than checking compliance boxes
- Standardization can be retrofitted later if organic demand emerges

**Revisit Trigger**: Three or more independent teams request conformance checking capabilities.

---

### D2: Level 2 Reference Implementation

**Status**: Decided
**Date**: 2026-01-08

**Decision**: Ship a Level 2 reference implementation as a separate repository.

**Rationale**:
- Concrete examples accelerate adoption more than abstract documentation.
- A separate repository maintains Level 1's platform-agnostic nature.
- "Example" positioning prevents reference implementation from becoming prescriptive.
- Starting narrow (GitHub Actions only) allows focused quality over broad coverage.

**Implications**:
- Reference implementation lives in its own repository, not Level 1
- Explicitly labeled as "example implementation" throughout
- Narrow initial scope: scaffold, lint, evidence assembly
- GitHub Actions only initially (most accessible CI platform)
- Versioned as v0.x to signal breaking changes are allowed
- Level 1 documentation may reference the implementation but does not require it

**Revisit Trigger**: Three or more GitHub Issues requesting multi-platform support (Azure DevOps, GitLab CI, etc.).

---

### D3: Tooling Contract Timing

**Status**: Decided
**Date**: 2026-01-08

**Decision**: No published tooling contract yet. Build first, observe real usage, extract patterns later.

**Rationale**:
- Premature abstraction creates wrong abstractions.
- Real implementations reveal actual integration points better than speculation.
- Multiple independent implementations showing convergent patterns provide confidence.
- Slower standardization traded for correctness is worthwhile at this stage.

**Implications**:
- No formal tooling API or contract in v1.0
- Reference implementation may change substantially based on learnings
- Third-party tools integrate at their own risk until contract stabilizes
- Pattern extraction happens after observing 2-3 real implementations

**Revisit Trigger**: Two to three independent implementations show convergent patterns in tooling integration.

---

### D4: Evidence Package

**Status**: Decided
**Date**: 2026-01-08

**Decision**: Formalize Evidence Package as a first-class concept at Level 1.

**Rationale**:
- Auditability is a core AOSD principle; evidence packages operationalize it.
- Consistent structure enables tooling and cross-project comparisons.
- Separating required contents from storage mechanism maintains Level 1/Level 2 boundary.
- Raw prompt exclusion by default respects privacy while enabling audit when needed.

**Implications**:
- Evidence Package concept defined in AOSD_FRAMEWORK.md
- Required contents specified (what must be captured)
- Storage mechanism is explicitly a Level 2 decision
- No raw prompts included by default (privacy-first)
- High-audit mode available as opt-in at Level 2
- Evidence packages represent the state of proof at the time of change and are not retroactively edited

---

### D5: AI Provenance

**Status**: Decided
**Date**: 2026-01-08

**Decision**: Metadata-only provenance by default, with opt-in high-audit mode.

**Rationale**:
- Full prompt logging raises privacy and IP concerns.
- Metadata captures essential provenance without sensitive content.
- High-audit environments (compliance, security-critical) need the option for full logging.
- Default should be safe; enhanced logging should be explicit choice.

**Implications**:
- Default provenance captures: model/version, agent role, timestamp, intent reference
- Raw prompts explicitly excluded from default evidence packages
- Level 2 implementations can enable high-audit mode when required
- Organizations choose their provenance depth based on requirements

---

### D6: Runtime Scope

**Status**: Decided
**Date**: 2026-01-08

**Decision**: Expand runtime concepts now, defer enforcement tooling.

**Rationale**:
- Runtime behavior matters for production systems; the framework should acknowledge this.
- Failure modes in slice specs capture runtime expectations at design time.
- Runtime invariants as a concept enables thinking about production behavior.
- Enforcement infrastructure is substantial investment; wait for demonstrated need.

**Implications**:
- Failure Modes section added to slice spec template
- Runtime Invariants documented as a concept in AOSD_FRAMEWORK.md
- No enforcement tooling or runtime monitoring in v1.0
- Conceptual foundation laid for future runtime capabilities

**Revisit Trigger**: Demonstrated need for runtime invariant enforcement tooling from active adopters.

---

## Decision Log

| ID | Title | Status | Date |
|----|-------|--------|------|
| [D1](#d1-framework-vs-standard) | Framework vs. Standard | Decided | 2026-01-08 |
| [D2](#d2-level-2-reference-implementation) | Level 2 Reference Implementation | Decided | 2026-01-08 |
| [D3](#d3-tooling-contract-timing) | Tooling Contract Timing | Decided | 2026-01-08 |
| [D4](#d4-evidence-package) | Evidence Package | Decided | 2026-01-08 |
| [D5](#d5-ai-provenance) | AI Provenance | Decided | 2026-01-08 |
| [D6](#d6-runtime-scope) | Runtime Scope | Decided | 2026-01-08 |
