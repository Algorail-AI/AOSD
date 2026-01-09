# AOSD Governance

This document describes how the AOSD framework is governed, maintained, and evolved.

---

## Stewardship

**Algorail AI** is the steward of the AOSD framework. This means:

- Algorail AI maintains the canonical AOSD repository
- Algorail AI sets the strategic direction and roadmap
- Major decisions about framework evolution are made by Algorail AI
- The framework reflects Algorail AI's experience building AI-assisted systems

**This is not governance by committee.** AOSD is not a crowdsourced project with democratic decision-making. It's a methodology developed through practice, maintained by those who use it daily.

---

## Decision Making

### Types of Decisions

| Decision Type | Authority | Process |
|---------------|-----------|---------|
| Typos, clarifications | Anyone via PR | Direct merge after review |
| New examples | Anyone via PR | Review for quality and fit |
| Documentation improvements | Anyone via PR | Review for accuracy |
| New patterns | Algorail AI | Proposal → validation → integration |
| Invariant changes | Algorail AI | Full evolution protocol |
| Architectural changes | Algorail AI | Extensive analysis and validation |

### Decision Criteria

When evaluating changes, we consider:

1. **Practical validation** — Has this been tested in real projects?
2. **Platform neutrality** — Does this maintain AOSD's deployment-agnostic stance?
3. **Clarity** — Does this make the framework easier to understand and apply?
4. **Consistency** — Does this align with existing principles?
5. **Minimalism** — Does this add essential value without bloat?

---

## Roadmap

The AOSD roadmap is **not crowdsourced**.

We welcome input and suggestions through GitHub Issues, but:

- Feature requests don't guarantee implementation
- The roadmap reflects Algorail AI's priorities and experience
- We may decline suggestions that don't fit the framework's direction

This isn't dismissive—it's focused. AOSD works because it's opinionated and coherent, not because it tries to be everything to everyone.

---

## Community Engagement

### What We Welcome

- **Bug reports** — Found an error? Let us know
- **Questions** — Confused about something? Ask
- **Experience reports** — Using AOSD? Tell us how it's going
- **Suggestions** — Ideas for improvement are always considered
- **Documentation fixes** — Typos, broken links, unclear language

### How to Engage

1. **GitHub Issues** — Primary channel for bugs, questions, and suggestions
2. **Pull Requests** — For documentation fixes and examples (see [CONTRIBUTING.md](CONTRIBUTING.md))
3. **Discussions** — For broader conversations about AOSD adoption

---

## Evolution Process

AOSD evolves through practice, not theory. Changes to the framework follow this process:

1. **Identify need** — Gap discovered through real project work
2. **Research** — Study patterns and alternatives
3. **Draft** — Propose specific changes
4. **Validate** — Test in actual projects
5. **Integrate** — Add to framework if proven useful
6. **Document** — Update relevant sections and modules

Major changes (new invariants, architectural shifts) require more extensive validation and may take significant time.

---

## Versioning

AOSD uses semantic versioning:

- **Major** (X.0.0) — Breaking changes to core principles
- **Minor** (0.X.0) — New features, patterns, or significant additions
- **Patch** (0.0.X) — Fixes, clarifications, examples

See [CHANGELOG.md](CHANGELOG.md) for version history.

---

## Questions?

If you're unsure about governance, contribution, or anything else:

1. Check [CONTRIBUTING.md](CONTRIBUTING.md) for contribution guidelines
2. Open a GitHub Issue with your question
3. Review existing issues and discussions

---

*This governance model reflects AOSD's nature as a practical methodology developed through experience, not a community-driven specification.*
