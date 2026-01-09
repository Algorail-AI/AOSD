# AOSD Meta-Guide

**AOSD for Maintaining AOSD**

This document defines how to maintain the AOSD Framework itself using AOSD principles. It ensures that changes propagate correctly across all dependent artifacts, preventing documentation drift.

---

## Core Principle

**No update is complete unless all dependent artifacts are updated.**

The AOSD Framework is not a single document—it's a coordinated set of artifacts that must remain consistent. A change to one artifact often requires changes to others.

---

## Artifact Dependency Map

The AOSD Framework consists of interconnected artifacts. Understanding these dependencies is essential for complete updates.

```
                    ┌─────────────────────┐
                    │  AOSD_FRAMEWORK.md  │
                    │   (Source of Truth) │
                    └──────────┬──────────┘
                               │
           ┌───────────────────┼───────────────────┐
           │                   │                   │
           ▼                   ▼                   ▼
    ┌──────────────┐   ┌──────────────┐   ┌──────────────┐
    │   SYLLABUS/  │   │  TEMPLATES/  │   │   examples/  │
    │  (Training)  │   │  (Reusable)  │   │ (Reference)  │
    └──────────────┘   └──────────────┘   └──────────────┘
           │                   │                   │
           └───────────────────┼───────────────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │     AI_GUIDE.md     │
                    │  (AI Consumption)   │
                    └─────────────────────┘
```

### Primary Artifacts

| Artifact | Purpose | Location |
|----------|---------|----------|
| **AOSD_FRAMEWORK.md** | Source of truth for methodology | `MANUAL/` |
| **SYLLABUS/** | Training curriculum (Modules 01-11) | Root |
| **TEMPLATES/** | Reusable templates for practitioners | Root |
| **examples/** | Reference implementations | `MANUAL/examples/reference-implementations/` |
| **AGENT.md** | Tool-agnostic repo operator contract | Root |
| **AI_GUIDE.md** | AI agent guide for applying AOSD methodology | Root |

### Supporting Artifacts

| Artifact | Purpose | Location |
|----------|---------|----------|
| **IMPLEMENTATION_TEMPLATE.md** | Level 2 profile creation | `MANUAL/` |
| **TROUBLESHOOTING.md** | Common issues and solutions | `MANUAL/` |
| **DEVELOPMENT_WORKFLOW.md** | Workflow principles | `MANUAL/` |
| **ENVIRONMENT_STRATEGY.md** | Environment isolation | `MANUAL/` |
| **MODEL_ROLES.md** | AI agent role definitions | `MANUAL/` |
| **AI_PROMPTING_PATTERNS.md** | Prompting principles | `MANUAL/` |
| **LESSONS_LEARNED.md** | Working memory for emerging patterns | `MANUAL/` |
| **RAG_GUIDE.md** | Retrieval system implementation guidance | `MANUAL/` |
| **CHANGELOG.md** | Version history | Root |

### Non-Normative Artifacts

| Artifact | Purpose | Location |
|----------|---------|----------|
| **AOSD_INFLUENCES_AND_ACKNOWLEDGMENTS.md** | Historical context, influences, citations | Root |

**Note**: Non-normative artifacts provide context and lineage but do not define methodology requirements. They can be updated independently without triggering cascading changes to normative artifacts.

---

## Dependency Rules

### When AOSD_FRAMEWORK.md Changes

The framework is the source of truth. Changes here cascade to multiple artifacts:

| Change Type | Affected Artifacts | Action Required |
|-------------|-------------------|-----------------|
| **New principle added** | SYLLABUS, AI_GUIDE | Add training module, update quick reference |
| **Principle modified** | SYLLABUS, TEMPLATES, examples | Update all references |
| **New goal added** | SYLLABUS, AI_GUIDE | Add training content, update quick reference |
| **New pattern added** | TEMPLATES, examples | Create template if applicable, add example |
| **Section renamed** | AI_GUIDE, SYLLABUS | Update all cross-references |
| **Version bumped** | CHANGELOG | Document changes |

### When SYLLABUS Changes

| Change Type | Affected Artifacts | Action Required |
|-------------|-------------------|-----------------|
| **New module added** | README.md, AGENT.md | Update repository structure |
| **Module content updated** | None (if aligned) | Verify alignment with framework |
| **Module removed** | README.md, AGENT.md | Update repository structure |

### When TEMPLATES Change

| Change Type | Affected Artifacts | Action Required |
|-------------|-------------------|-----------------|
| **New template added** | AI_GUIDE | Update templates quick reference |
| **Template modified** | Examples (if using template) | Verify examples still align |
| **Template removed** | AI_GUIDE, examples | Remove all references |

### When Examples Change

| Change Type | Affected Artifacts | Action Required |
|-------------|-------------------|-----------------|
| **New example added** | AGENT.md (if significant) | Update structure if needed |
| **Example modified** | None (usually isolated) | Verify alignment with framework |
| **Example removed** | Any references | Remove cross-references |

### When AGENT.md Changes

| Change Type | Affected Artifacts | Action Required |
|-------------|-------------------|-----------------|
| **Repo governance updated** | README.md, AI_GUIDE | Verify references still correct |
| **Principles summary updated** | Verify AOSD_FRAMEWORK | Ensure consistency |
| **Structure section updated** | README.md | Ensure repository structure matches |

### When AI_GUIDE.md Changes

| Change Type | Affected Artifacts | Action Required |
|-------------|-------------------|-----------------|
| **Quick reference updated** | Verify AOSD_FRAMEWORK | Ensure consistency |
| **New section added** | AGENT.md (if relevant to repo operation) | Update reference if needed |

### When LESSONS_LEARNED.md Changes

| Change Type | Affected Artifacts | Action Required |
|-------------|-------------------|-----------------|
| **Entry graduates** | Destination artifact (TROUBLESHOOTING, INVARIANTS, etc.) | Add content to destination, update entry status |
| **Entry archived** | None | Update entry status only |
| **New entry added** | None | No cascade (entries are non-authoritative) |

**Note**: LESSONS_LEARNED.md is explicitly non-authoritative. Entries do not cascade to other artifacts until graduation. The 30-day rule ensures entries are either promoted or archived.

---

## Update Checklist

Use this checklist when making changes to the AOSD Framework:

### Before Making Changes

- [ ] Identify which artifact is being changed
- [ ] Review dependency map for affected artifacts
- [ ] Check if this is a framework change (cascades most)
- [ ] Plan updates to all affected artifacts

### Making Framework Changes (AOSD_FRAMEWORK.md)

- [ ] Make the change to AOSD_FRAMEWORK.md
- [ ] Bump version number (MAJOR.MINOR.PATCH)
- [ ] Update CHANGELOG.md with change description
- [ ] Check SYLLABUS modules for affected content
- [ ] Check TEMPLATES for affected templates
- [ ] Check examples for affected implementations
- [ ] Update AI_GUIDE.md quick references if needed

### Making Template Changes

- [ ] Make the change to the template
- [ ] Update AI_GUIDE.md templates quick reference
- [ ] Check examples that use this template

### Making Syllabus Changes

- [ ] Verify alignment with AOSD_FRAMEWORK.md
- [ ] Update module cross-references if needed

### Making Example Changes

- [ ] Verify example aligns with current framework
- [ ] Check for cross-references that need updating
- [ ] Ensure example follows current patterns

### After All Changes

- [ ] Run a consistency check across artifacts
- [ ] Verify all cross-references work
- [ ] Ensure CHANGELOG.md is updated with all changes
- [ ] Commit all related changes together
- [ ] Document the change scope in commit message

### CHANGELOG Entry Guidelines

All changes should be documented following [Keep a Changelog](https://keepachangelog.com/):

| Category | Use For |
|----------|---------|
| **Added** | New features, sections, documents |
| **Changed** | Modifications to existing content |
| **Deprecated** | Features marked for removal |
| **Removed** | Deleted content |
| **Fixed** | Bug fixes, corrections |
| **Security** | Security-related changes |

Each entry should include:
- Clear description of what changed
- File/location reference (e.g., "AOSD_FRAMEWORK.md")
- Sub-bullets for significant details

---

## Self-Maintenance Principles

### Principle 1: Single Source of Truth

**AOSD_FRAMEWORK.md is authoritative.** Other artifacts derive from it, reference it, or apply it. If there's a conflict, the framework wins.

Implications:
- Never introduce new principles in SYLLABUS without adding to framework first
- Never create templates that contradict framework patterns
- Never create examples that violate framework invariants

### Principle 2: Atomic Updates

**Related changes must be committed together.** A framework change without its cascading updates creates drift.

Bad:
```
Commit 1: Add new principle to AOSD_FRAMEWORK.md
Commit 2 (days later): Update SYLLABUS for new principle
Commit 3 (weeks later): Update AI_GUIDE quick reference
```

Good:
```
Commit 1: Add new principle with all dependent updates
  - AOSD_FRAMEWORK.md (new principle)
  - SYLLABUS/MODULE_XX (training content)
  - AI_GUIDE.md (quick reference)
  - CHANGELOG.md (version bump)
```

### Principle 3: Version Discipline

**Every framework change bumps the version.** This creates a clear audit trail.

- **MAJOR**: Breaking changes to principles or structure
- **MINOR**: New principles, patterns, or sections
- **PATCH**: Clarifications, fixes, improvements

### Principle 4: Cross-Reference Integrity

**All cross-references must be verified.** Broken links and stale references erode trust.

Check:
- Markdown links between documents
- Section references (e.g., "See Section X")
- Principle/Goal numbers
- Template file paths

### Principle 5: Example Alignment

**Examples must demonstrate current patterns.** Outdated examples teach wrong patterns.

When framework changes:
- Review all examples for alignment
- Update or deprecate misaligned examples
- Add new examples for new patterns

### Principle 6: Training Synchronization

**SYLLABUS must match current framework.** Training outdated content creates confusion.

When framework changes:
- Identify affected modules
- Update module content
- Update module cross-references
- Verify learning objectives still align

---

## Common Drift Patterns

### Pattern 1: Framework-Only Updates

**Symptom**: Framework updated, but SYLLABUS/AI_GUIDE not updated.
**Detection**: Quick reference in AI_GUIDE doesn't match framework.
**Fix**: Update all dependent artifacts before merging.

### Pattern 2: Template Divergence

**Symptom**: Templates no longer match framework patterns.
**Detection**: New principles not reflected in templates.
**Fix**: Audit templates against framework patterns quarterly.

### Pattern 3: Stale Examples

**Symptom**: Examples use deprecated patterns.
**Detection**: Example doesn't compile/work with current guidance.
**Fix**: Update example or mark as deprecated.

### Pattern 4: Orphaned Cross-References

**Symptom**: Links point to renamed/moved sections.
**Detection**: Broken links in rendered markdown.
**Fix**: Use search to find all references before renaming.

### Pattern 5: Version Drift

**Symptom**: Version number doesn't reflect actual changes.
**Detection**: CHANGELOG doesn't match version bumps.
**Fix**: Establish version discipline as mandatory.

---

## AI-Assisted Maintenance

AI agents can help maintain consistency:

### Consistency Checks

Ask AI to:
- "Compare AI_GUIDE.md quick reference against AOSD_FRAMEWORK.md principles"
- "List all cross-references in SYLLABUS that point to AOSD_FRAMEWORK.md"
- "Check if all templates align with current framework patterns"

### Impact Analysis

Before making changes, ask AI to:
- "What artifacts would be affected if I add a new principle?"
- "What would need to change if I rename the 'Slice-Based Development' section?"
- "What examples reference the Environment Factory pattern?"

### Update Generation

After framework changes, ask AI to:
- "Update AI_GUIDE.md quick reference to match new framework"
- "Generate CHANGELOG entry for this version bump"
- "Update SYLLABUS module to cover new principle"

---

## Maintenance Workflow

### For Minor Updates (Patches)

1. Make the change
2. Verify no cross-reference breaks
3. Commit with descriptive message
4. No version bump required for patches to supporting docs

### For Feature Updates (Minor Version)

1. Plan the change and identify dependencies
2. Make all changes in a single branch
3. Run consistency checks
4. Bump MINOR version
5. Update CHANGELOG
6. Commit all together
7. Create PR with clear scope description

### For Breaking Changes (Major Version)

1. Document the breaking change thoroughly
2. Plan migration guidance
3. Update ALL artifacts
4. Bump MAJOR version
5. Create detailed CHANGELOG entry
6. Consider deprecation period if possible
7. Announce to practitioners

---

## Quick Reference: What Updates What

| If You Change... | You Must Also Update... |
|------------------|------------------------|
| A principle | SYLLABUS, AI_GUIDE, AGENT.md (principles summary), CHANGELOG |
| A goal | SYLLABUS, AI_GUIDE, CHANGELOG |
| A pattern | TEMPLATES (if applicable), examples, CHANGELOG |
| Terminology | AI_GUIDE terminology table, SYLLABUS |
| Section names | All cross-references, AI_GUIDE, AGENT.md |
| File structure | AGENT.md, README.md |
| Repo governance | AGENT.md, README.md |
| Templates | AI_GUIDE templates reference |
| Examples | Cross-references if any |

---

## Invariant Pressure Assessment

This section defines **when and how AOSD should evolve** in response to technological change, operational experience, and failure.

### Core Principle: Failure > Hype

Framework evolution is driven by **invariant pressure and observed failure**, not external novelty.

| Signal Type | Weight | Examples |
|-------------|--------|----------|
| **Internal friction** | High | Invariant blocks valid work, workflow creates repeated pain |
| **Observed failure** | High | Principle didn't prevent a real problem it was designed to prevent |
| **External capability** | Low | New model released, new tool announced |
| **Industry discourse** | Lowest | Blog posts, conference talks, social media trends |

**Rule**: A framework that evolves from its own failures remains relevant longer than one that evolves from external discourse.

### The Pressure Question

Before considering any framework change, ask:

> **Does this development violate, weaken, strengthen, or leave unchanged an existing AOSD invariant or principle?**

Possible outcomes:

| Classification | Meaning | Action |
|----------------|---------|--------|
| **Irrelevant** | Does not affect any invariant | No action (default) |
| **Clarifying** | Reveals ambiguity in existing principle | Consider PATCH |
| **Extending** | Adds new capability consistent with principles | Consider MINOR |
| **Breaking** | Challenges or contradicts a core principle | Requires MAJOR + explicit rationale |

Most external signals should be classified as **Irrelevant**. This is healthy.

### Change Taxonomy

Changes to AOSD are classified by scope and mapped to versioning:

| Change Type | Description | Version Impact |
|-------------|-------------|----------------|
| **Clarification** | Improves wording, examples, or structure without behavioral change | PATCH |
| **Extension** | Adds new concepts consistent with existing principles | MINOR |
| **Breaking Conceptual Change** | Alters or replaces a core principle or invariant | MAJOR |

Breaking changes require:
- Explicit entry in `DECISIONS.md` with rationale
- Evidence of invariant pressure (not just preference)
- Human approval (AI agents advise, humans decide)

### Governance Boundary

**AI agents can**:
- Observe friction and suggest it be recorded
- Analyze whether a signal affects invariants
- Draft proposed changes for human review
- Challenge assumptions and ask clarifying questions

**Only humans can**:
- Decide whether a signal merits action
- Approve Level 1 (framework) changes
- Update `DECISIONS.md`
- Classify a change as Breaking

This boundary ensures AOSD remains **human-governed** even as AI assists with maintenance.

### Framework Stress Events

When friction or failure reveals potential pressure on AOSD principles, open a **Framework Stress Event** issue (see `.github/ISSUE_TEMPLATE/framework_stress_event.md`).

A stress event captures:
- What happened (the friction or failure)
- Which invariant/principle is involved
- Evidence or context
- Whether this is a one-off or repeated pattern

Stress events are the **pull-based** mechanism for framework evolution. There are no scheduled reviews or autonomous scanning—evolution is triggered by observed pain.

---

## Summary

Maintaining AOSD requires the same discipline that AOSD applies to software:

1. **Single source of truth** - AOSD_FRAMEWORK.md is authoritative
2. **Atomic updates** - Related changes committed together
3. **Version discipline** - Every change tracked and versioned
4. **Cross-reference integrity** - All links verified
5. **Example alignment** - Examples match current patterns
6. **Training synchronization** - SYLLABUS matches framework

**The meta-principle**: Apply AOSD to AOSD itself.

---

**End of META_GUIDE.md**
