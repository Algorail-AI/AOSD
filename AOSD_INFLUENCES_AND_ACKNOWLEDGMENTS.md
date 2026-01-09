# AOSD Influences and Acknowledgments

**Non-Normative Companion to the AOSD Framework**

---

## Scope of This Document

This document captures influences that informed the design of AOSD. It is **not** a complete bibliography, nor does inclusion imply endorsement, dependency, or requirement. The AOSD methodology is self-contained and does not require familiarity with any referenced work to understand or apply it.

**This document is explicitly non-normative.** The validity of AOSD does not depend on the continued availability of any referenced external material.

---

## 1. Acknowledgments

### Human Orchestrator

The AOSD methodology was conceived, authored, and maintained by **Jim Wiedman**, who serves as the Human Orchestrator for this framework. All editorial decisions, architectural choices, and methodology direction remain under human authority.

### AI Collaborators

AOSD was co-created with AI agents, specifically **Claude** (Anthropic) and **ChatGPT** (OpenAI). These AI collaborators participated in:

- Drafting and refining documentation
- Critiquing methodology choices
- Suggesting alternative approaches
- Identifying gaps and inconsistencies
- Generating examples and reference implementations

**Important clarification**: AI agents participated as collaborative tools under human direction. The Human Orchestrator retains final editorial authority over all content. This collaboration models the same human-AI orchestration pattern that AOSD teaches.

### Community Contributors

AOSD benefits from feedback and refinements contributed by practitioners who have applied the methodology. Specific contributions are tracked through the repository's issue tracker and commit history.

---

## 2. Influences and Prior Art

The following works, projects, and ideas influenced AOSD's design. Inclusion does not imply that AOSD implements or endorses any specific approach; rather, these are acknowledged as sources of inspiration or contrast.

### Industry and Tooling Influences

| Influence | Contribution to AOSD |
|-----------|----------------------|
| **AI Engineering Podcast** (Tobias Macey & Maxime Beauchemin) | Early exploration of AI-assisted engineering practices |
| **Agor** (multi-agent orchestration framework) | Spatial orchestration concepts, session trees, zone-based agent organization |
| **Claude Code, Cursor, GitHub Copilot** | IDE-driven AI development experiences that shaped practical workflow understanding |
| **Claude Agent SDK** | Patterns for structured agent orchestration and tool use |

### Engineering and Architecture Influences

| Influence | Contribution to AOSD |
|-----------|----------------------|
| **NIST SP 800-160** (Systems Security Engineering) | Security engineering principles, trustworthiness concepts, defense-in-depth |
| **Functional Core, Imperative Shell** (Gary Bernhardt) | Architectural pattern for separating pure logic from side effects |
| **GitOps / Infrastructure-as-Code** | Declarative configuration, version-controlled infrastructure, audit trails |
| **Test Pyramid** (Mike Cohn) and testing practices | Fast/medium/slow test tier design, feedback loop optimization |
| **Agile / Continuous Delivery** (Humble & Farley) | Slice-based incremental delivery, fast feedback loops, small batch sizes |
| **Architecture Decision Records** (Michael Nygard) | ADR lineage, decision traceability, documenting "why" not just "what" |
| **Trunk-Based Development** | Branch management patterns, continuous integration practices |
| **Spec-Driven Development** | Lessons Learned artifact pattern, structured capture of operational learning with graduation paths; critical analysis by Birgitta Böckeler (Thoughtworks) informed AOSD's divergences |

### Internal Experience

AOSD's pragmatic design decisions emerged from real-world production constraints:

- Multi-team orchestration challenges
- CMMC and FedRAMP security compliance requirements
- Solo orchestrator to multi-orchestrator scaling patterns
- Cost optimization under constrained budgets
- Legacy system integration and incremental modernization
- AI cost management and token efficiency concerns

---

## 3. Divergence from Influences

AOSD intentionally differs from certain patterns observed in referenced works and common industry practices. These divergences reflect AOSD's core philosophy.

### From Agor and Similar Multi-Agent Frameworks

| Pattern | AOSD's Divergence | Rationale |
|---------|-------------------|-----------|
| Shared environments across agents | Isolated environments per agent | Prevents cross-contamination, ensures reproducibility |
| Unbounded agent autonomy | Constrained autonomy via trust levels | Predictability and auditability over raw speed |
| Peer-to-peer agent communication | Human-mediated coordination | Maintains human oversight and control |

### From Common AI Coding Practices

| Pattern | AOSD's Divergence | Rationale |
|---------|-------------------|-----------|
| Prompt-only control | Invariants + prompts + structure | Prompts alone cannot enforce long-term constraints |
| Context maximization | Just-in-time context loading | Token efficiency, focused attention, reduced drift |
| Speed-first development | Correctness-first development | Sustainable velocity over burst productivity |
| Implicit architectural decisions | Explicit ADRs and documented choices | Traceability and maintainability |

### From Spec-Driven Development (SDD)

Spec-Driven Development is an emerging approach in AI-assisted software development where structured specifications guide or constrain code generation. AOSD was informed by the critical exploration of SDD tools and workflows by Birgitta Böckeler (Thoughtworks), published on martinfowler.com. Her analysis explores variants ranging from spec-first workflows to spec-as-source systems, while highlighting significant practical concerns.

AOSD acknowledges SDD as an influence—particularly the Lessons Learned artifact pattern for capturing operational learning with structured graduation paths. AOSD shares SDD's emphasis on explicit intent and traceability. However, AOSD intentionally diverges from SDD's core methodology in several ways:

| SDD Pattern | AOSD's Divergence | Rationale |
|-------------|-------------------|-----------|
| Specifications as primary/authoritative artifact | Specifications as lightweight, scoped guides | Aligned with Böckeler's critique of "reviewing markdown over code"—AOSD favors reviewing implementations over verbose up-front documentation |
| Spec completeness drives correctness | Human orchestration + invariants + runtime validation | Correctness emerges from practice, not spec coverage |
| Full up-front specification | Progressive elaboration per slice | AOSD avoids the "sledgehammer" effect by scaling documentation rigor to the risk tier of the specific task |
| Spec-as-source-of-truth control | Human orchestrator as source of authority | Maintains human judgment at the center; avoids brittleness of spec-as-source systems |

**Key distinction**: SDD-style tools or workflows may be used *within* AOSD where appropriate, but AOSD itself is not a spec-driven methodology. AOSD is designed for sustained evolution and handoff, not correctness-by-construction alone.

### Core Philosophy

**AOSD optimizes for sustainability, correctness, and defensibility—not maximum speed at all costs.**

This means accepting some development velocity constraints in exchange for:
- Systems that outlive their original developers
- Clear audit trails for security and compliance
- Predictable behavior from AI agents
- Lower long-term maintenance costs

---

## 4. Related but Out-of-Scope

The following bodies of work are related to AOSD but intentionally out of scope or only partially addressed by the current methodology:

| Topic | Relationship to AOSD |
|-------|----------------------|
| **Formal Methods / Specification-Driven Development** | AOSD does not require formal proofs; invariants are enforced through practice and review |
| **Model-Based Systems Engineering (MBSE)** | AOSD focuses on software development; systems engineering integration is environment-specific |
| **Full Systems Security Engineering** | AOSD incorporates security principles but does not replace comprehensive security programs |
| **Machine Learning Operations (MLOps)** | AI model training and deployment pipelines are implementation-specific (Level 2/3) |
| **Organizational Change Management** | AOSD provides methodology but not organizational adoption strategies |

These boundaries help keep AOSD focused on its core purpose: enabling humans to orchestrate AI agents effectively for software development.

---

## 5. How to Cite AOSD

If AOSD influences your work, please cite it. We cite our influences and encourage the same in return.

### Recommended Citation

```
Wiedman, J. (2025). AOSD: AI Orchestrated Systems Development Framework.
https://github.com/Algorail-AI/AOSD
```

### BibTeX Format

```bibtex
@misc{wiedman2025aosd,
  author = {Wiedman, Jim},
  title = {AOSD: AI Orchestrated Systems Development Framework},
  year = {2025},
  publisher = {GitHub},
  howpublished = {\url{https://github.com/Algorail-AI/AOSD}},
  note = {Co-created with AI collaborators Claude and ChatGPT}
}
```

### Attribution for Derived Works

If you create derivative works or adaptations based on AOSD:

1. Acknowledge AOSD as a foundational influence
2. Clearly indicate which portions are derived vs. original
3. Consider contributing improvements back to the AOSD community

---

## 6. Disclaimer

### Non-Normative Status

This document is **non-normative**. It provides historical context and intellectual lineage but does not define methodology requirements. The normative AOSD specification is contained in `MANUAL/AOSD_FRAMEWORK.md`.

### Independence from External Sources

The AOSD methodology **does not depend** on any external tool, project, or specification listed in this document. AOSD is designed to be self-contained and understandable without reference to its influences.

### Link Stability

External references may become unavailable over time. **Breaking links do not affect the validity of the AOSD specification.** This document captures influences at a point in time; the methodology stands on its own regardless of external resource availability.

### No Endorsement

Listing a project, paper, or tool as an influence does not constitute endorsement of that work, nor does it imply any formal relationship between AOSD and the referenced work.

---

**End of AOSD_INFLUENCES_AND_ACKNOWLEDGMENTS.md**
