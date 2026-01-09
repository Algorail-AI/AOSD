# AOSD Lessons Learned

**Working Memory for Operational Learnings**

This document captures practical friction and workarounds that are not yet stable enough to become formal troubleshooting guidance, invariants, or anti-patterns. It serves as a staging area with enforced graduation or archival.

---

## Purpose

Lessons Learned bridges the gap between:
- **Framework Stress Events** (too heavy for operational learning)
- **TROUBLESHOOTING / INVARIANTS** (too permanent for emerging patterns)

This is **organizational working memory with enforced promotion or deletion**.

---

## Non-Authoritative Status

> **Lessons Learned entries are explicitly non-authoritative.**
>
> This document cannot be cited as guidance. Entries here are observations in probation, not established doctrine. For authoritative guidance, see [TROUBLESHOOTING.md](./TROUBLESHOOTING.md), [AOSD_FRAMEWORK.md](./AOSD_FRAMEWORK.md), or your organization's invariants.

---

## Lifecycle Rules

### 30-Day Rule

> **Active entries must graduate or archive within 30 days of capture.**

This prevents document rot. If a pattern isn't stable enough to graduate after 30 days, it should be archived or escalated as a Framework Stress Event.

### Status Definitions

| Status | Meaning |
|--------|---------|
| **Active** | Under observation, not yet validated as stable pattern |
| **Graduated** | Promoted to permanent artifact (see destination) |
| **Archived** | Determined to be one-off, invalid, or superseded |

---

## Graduation Destinations

When an Active lesson stabilizes, it graduates to one of these destinations:

| Destination | When to Use | Action |
|-------------|-------------|--------|
| **TROUBLESHOOTING.md** | Symptom → solution pattern that helps others | Add new section with solution |
| **ANTI_PATTERNS.md** | Thing to avoid that causes repeated harm | Add new anti-pattern entry |
| **Invariants (L1 or L2)** | Hard rule that must be enforced | Add to AOSD_FRAMEWORK.md or L2 profile |
| **L2 Profile** | Platform/context-specific guidance | Add to organization's Orchestration Profile |
| **Framework Stress Event** | Pattern challenges AOSD itself | Open issue using stress event template |

**Note**: Archived lessons may still provide context. The archive section preserves learnings that didn't graduate but may inform future decisions.

---

## Entry Template

```markdown
### LL-###: [Title]

- **Captured**: YYYY-MM-DD
- **Context**: [Where/when this happened - project, phase, circumstance]
- **Symptom**: [What went wrong or caused friction]
- **Fix**: [What worked or what workaround was applied]
- **Destination**: [Where this should graduate if pattern holds]
- **Status**: Active | Graduated | Archived
```

---

## Active Lessons

### LL-001: Authority Drift in Multi-Agent Deployments

- **Captured**: 2025-12-29
- **Context**: Multi-agent parallel development with concurrent deployment operations
- **Symptom**: Work was lost when a deployment from one session overwrote a fix that existed only in a feature branch. The fix appeared to work, was deployed, but was never merged to main. A subsequent deployment from main reverted the fix. The surface symptom was "two agents worked at the same time" but the root cause was deeper.
- **Root Cause**: The system allowed a deployment that was not traceable to an authoritative branch state. The invariant "only main represents deployable truth" existed implicitly but was not enforced socially, procedurally, or mechanically.
- **Key Insight**: Multi-agent development didn't create this failure mode—it compressed time and removed the illusion of safety. In single-human workflows, the same failure happens slower (hotfix locally, deploy from wrong branch, assume it'll get merged later, another deploy overwrites it). AOSD surfaced a governance gap that was already latent.
- **Fix**:
  1. Add explicit pre-deploy invariant check: `git merge-base --is-ancestor HEAD origin/main`
  2. Encode branch authority into agent roles (Builder agents can work anywhere; Deploy operations only from main)
  3. Require deployment provenance stamps (commit, branch, PR, agent, timestamp)
  4. Auto-delete feature branches after merge to remove authority ambiguity
- **Meta-Lesson**: AOSD doesn't fail because agents are wrong. It fails when authority is implicit instead of encoded. Intelligence is cheap; coordination is the scarce resource; authority must be encoded, not assumed.
- **Destination**: INVARIANTS_COOKBOOK.md (new Deployment Authority section), MULTI_AGENT_PATTERNS.md (Authority and State Management), ANTI_PATTERNS.md (Implicit Authority)
- **Status**: Active

---

## Graduated Lessons

*Lessons that have been promoted to permanent artifacts.*

| ID | Title | Graduated To | Date |
|----|-------|--------------|------|
| - | - | - | - |

---

## Archived Lessons

*Lessons determined to be one-off, invalid, or superseded.*

| ID | Title | Reason | Date |
|----|-------|--------|------|
| - | - | - | - |

---

## How to Use This Document

### For AI Agents

When troubleshooting issues:
1. Check TROUBLESHOOTING.md first (authoritative)
2. Optionally scan Active Lessons for emerging patterns
3. If you discover new friction, suggest capturing it here

### For Human Orchestrators

When you encounter friction:
1. Capture it immediately (don't wait for perfect understanding)
2. Set a reminder to review in 2 weeks
3. By day 30, decide: Graduate, Archive, or Escalate

### Graduation Checklist

Before graduating a lesson, verify:
- [ ] Pattern observed more than once
- [ ] Fix is stable and reproducible
- [ ] Destination artifact is appropriate
- [ ] Entry provides enough context for others

---

## Related Documents

- [TROUBLESHOOTING.md](./TROUBLESHOOTING.md) - Authoritative solutions
- [AOSD_FRAMEWORK.md](./AOSD_FRAMEWORK.md) - Core methodology
- [META_GUIDE.md](./META_GUIDE.md) - Framework maintenance rules
- `.github/ISSUE_TEMPLATE/framework_stress_event.md` - For patterns that challenge AOSD

---

**End of LESSONS_LEARNED.md**
