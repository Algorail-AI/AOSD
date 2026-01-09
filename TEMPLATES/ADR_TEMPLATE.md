# ADR NNN: [Decision Title]

<!--
AI INGESTION GUIDELINES:
- This ADR follows the standard AOSD format for AI consumption
- AI agents should read Context and Decision sections first
- Consequences section informs impact analysis
- Cross-Artifact Sync section guides propagation requirements
- See AI_GUIDE.md for optimal ADR processing patterns
-->

---

## Work Item Reference

| Field | Value |
|-------|-------|
| **Work Item ID** | #[issue-number] |
| **Branch Name** | `feature/[number]-[description]` |
| **ADR Status** | Proposed / Accepted / Deprecated / Superseded |
| **Created** | [YYYY-MM-DD] |
| **Author** | [Jim / ChatGPT / Claude Code with human approval] |
| **Reviewers** | [Who reviewed this] |

*Link this ADR to the work item that tracks the decision implementation.*

---

## Context

<!-- What is the issue we're facing? -->
<!-- What are the forces at play? -->
<!-- What constraints do we have? -->

[Describe the problem, requirements, and constraints]

**Background**:
- Current state: [What exists today]
- Problem: [What's not working or what's needed]
- Constraints: [Technical, business, or regulatory limitations]

---

## Decision

<!-- What is the change we're proposing? -->
<!-- What architectural pattern are we adopting/changing? -->

**We will**: [Describe the decision clearly]

**Scope**:
- [What's included in this decision]
- [What's explicitly excluded]

**Implementation approach**:
- [High-level how this will be implemented]

---

## Consequences

### Positive

<!-- What becomes better? -->
- Improved [specific benefit]
- Enables [new capability]
- Reduces [specific problem]

### Negative

<!-- What becomes harder or more complex? -->
- Increased [specific complexity]
- Additional [maintenance burden, learning curve, etc.]
- Requires [new dependencies, infrastructure, etc.]

### Neutral

<!-- What changes without clear positive/negative? -->
- Changes [aspect] without improving or degrading it
- Shifts [responsibility/ownership] to [new location]

---

## Alternatives Considered

### Alternative 1: [Name]

**Description**: [What this approach would be]

**Pros**:
- [Benefit 1]
- [Benefit 2]

**Cons**:
- [Drawback 1]
- [Drawback 2]

**Why rejected**: [Specific reason we chose the main decision over this alternative]

### Alternative 2: [Name]

**Description**: [What this approach would be]

**Pros**:
- [Benefit 1]

**Cons**:
- [Drawback 1]

**Why rejected**: [Reason]

### Alternative 3: Do Nothing

**Description**: Keep current approach without changes

**Pros**:
- No implementation cost
- No migration needed

**Cons**:
- [Problem continues]
- [Missed opportunity]

**Why rejected**: [Why status quo is insufficient]

---

## AOSD Principles Alignment

<!-- How does this decision align with AOSD principles and goals? -->

**Goals Supported**:
- [ ] Goal 1: Build Sustainable Systems (maintainability, documentation, traceability)
- [ ] Goal 2: Security & Trustworthiness (security-by-design, compliance)
- [ ] Goal 3: Fast Evolution (incremental delivery, low-risk changes)
- [ ] Goal 4: Minimal Operational Cost (lean architecture, cost awareness)
- [ ] Goal 5: AI Integration (runtime AI capabilities)
- [ ] Goal 6: Embedded Training (in-app guidance, documentation)

**Security-by-Design Considerations**:
- [ ] Security controls addressed at architectural level
- [ ] Audit and traceability requirements considered
- [ ] Defense-in-depth principles applied
- [ ] Fail-safe defaults specified

**Scaling Considerations**:
- [ ] Impact on team workflows documented
- [ ] Multi-developer coordination considered
- [ ] Knowledge sharing implications addressed

**Restrictive Environment Considerations** (if applicable):
- [ ] Works in network-isolated/government environments
- [ ] Compliant with ITAR/export controls
- [ ] Applicable to on-premises deployments
- [ ] N/A - not relevant to this decision

---

## Implementation Notes

<!-- How will this be implemented? -->
<!-- What changes are required? -->
<!-- What's the migration path? -->

**Work Items**:
- Implementation Issue: #[issue-number]
- Related Issues: #[issue-number], #[issue-number]

**Implementation phases**:
1. Phase 1: [Description] → Issue #[number]
2. Phase 2: [Description] → Issue #[number]
3. Phase 3: [Description] → Issue #[number]

**Files affected**:
- `[file path]` - [what changes]
- `[file path]` - [what changes]

**Migration path** (if applicable):
- Existing code: [How does existing code transition]
- Data migration: [Any data changes needed]
- Timeline: [Estimated implementation time]

**Dependencies**:
- Requires: [What must exist first]
- Blocks: [What this blocks]
- Enables: [What this enables]

---

## Validation Criteria

<!-- How will we know this decision was correct? -->

**Success indicators**:
- [ ] [Measurable outcome 1]
- [ ] [Measurable outcome 2]
- [ ] [Measurable outcome 3]

**Failure indicators** (reconsider decision if these occur):
- [ ] [Negative outcome 1]
- [ ] [Negative outcome 2]

**Metrics to track**:
- [Performance metric]
- [Cost metric]
- [Quality metric]

**Review timeline**: [When to evaluate if this decision was correct - 3 months, 6 months, etc.]

---

## Related Decisions

<!-- Links to other ADRs this affects or is affected by -->

**Supersedes**:
- ADR-XXX: [Title] (if this replaces a previous decision)

**Related**:
- ADR-YYY: [Title] (if this interacts with another decision)

**Depends on**:
- ADR-ZZZ: [Title] (if this requires another decision first)

**Impacts**:
- ADR-AAA: [Title] (if this affects another decision)

---

## Cross-Artifact Sync Requirements

<!-- Per META_GUIDE.md: What other artifacts need updating when this decision is implemented? -->

**Documentation Updates Required**:
- [ ] AOSD_FRAMEWORK.md (if adding new patterns/principles)
- [ ] AI_GUIDE.md quick references (if terminology changes)
- [ ] SYLLABUS modules (if training content affected)

**Template Updates Required**:
- [ ] Slice Spec template (if new invariants added)
- [ ] Review Checklist (if new review criteria needed)
- [ ] TodoWrite template (if workflow changes)
- [ ] N/A - no template updates needed

**Example/Reference Updates Required**:
- [ ] Reference implementations affected
- [ ] Pattern library additions needed
- [ ] N/A - no examples affected

**Version Impact**:
- [ ] MAJOR version bump (breaking changes)
- [ ] MINOR version bump (new features/patterns)
- [ ] PATCH version bump (clarifications/fixes)
- [ ] N/A - no framework version impact

*See META_GUIDE.md for complete propagation rules.*

---

## References

<!-- Links to discussions, documentation, research -->

**Internal**:
- GitHub Issue #XXX: [Issue title]
- Framework section: [relevant framework section]
- Related PR #YYY: [PR title]

**External**:
- AWS Documentation: [URL]
- Blog post: [URL]
- Research paper: [URL]

**Discussion**:
- ChatGPT conversation: [Date, summary]
- Team discussion: [Date, participants, summary]

---

## Notes

<!-- Any additional context, caveats, or considerations -->

**Assumptions**:
- [Assumption 1 this decision relies on]
- [Assumption 2]

**Risks**:
- [Risk 1 and mitigation strategy]
- [Risk 2 and mitigation strategy]

**Future considerations**:
- [What might change this decision in the future]
- [When to revisit this ADR]

---

**Approval**: [Awaiting review / Approved by Jim on YYYY-MM-DD / Rejected]

**Implementation Status**: [Not started / In progress / Complete / Superseded]

**Implementation Tracking**: See Issue #[issue-number] for implementation progress
