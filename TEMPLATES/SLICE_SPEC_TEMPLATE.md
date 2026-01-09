# Slice Specification Template

<!--
AI INGESTION GUIDELINES:
- This slice spec follows AOSD vertical slice principles
- AI agents should read Goals/Non-Goals first to understand scope
- AOSD Invariants section defines non-negotiable constraints
- Security and Scaling sections require explicit consideration
- See AI_GUIDE.md for slice implementation patterns
-->

---

## Work Item Reference

| Field | Value |
|-------|-------|
| **Work Item ID** | #[issue-number] |
| **Branch Name** | `feature/[number]-[description]` |
| **Slice ID** | SLICE_[PROJECT]_[NUMBER] |
| **Created** | [YYYY-MM-DD] |
| **Status** | Draft / In Progress / Complete |
| **HITL Trust Level** | [0-5] - [Name] |

*Link this slice spec to the work item that tracks it.*

### HITL Trust Level Reference

| Level | Name | When to Use |
|-------|------|-------------|
| 0 | Full Human | Production deploys, security incidents, data deletion |
| 1 | AI Assists | Architecture decisions, invariant changes |
| 2 | AI Drafts | PR creation, documentation, slice specs |
| 3 | AI Executes | DB migrations, infra changes, dependency updates |
| 4 | AI Autonomous | Unit tests, formatting, routine refactoring |
| 5 | AI Silent | Linting, auto-formatting, spell checking |

*See Principle 8 (Human-in-the-Loop) in AOSD_FRAMEWORK.md for full guidance.*

---

**AOSD Development  Lightweight Slice Spec**

Use this template for small-to-medium increments of work ("slices"), typically completed in 12 cycles with Claude Code.

---

# 1. Slice Summary

What is the smallest valuable unit of work?
Describe in one sentence.

Example:
> Add decision preview UI to the control detail page.

---

# 2. Goals & Non-Goals

### Goals
- G1:
- G2:

### Non-Goals
- NG1:
- NG2:

Helps keep the slice small and focused.

---

# 3. Current Behavior vs Desired Behavior

### Current Behavior
Describe how things work today.

### Desired Behavior
Describe how things should work after the slice.

---

# 4. AOSD Invariants Involved

List which architectural invariants apply to this slice.

### Starter Invariants (from AOSD_FRAMEWORK.md)

Check which universal starter invariants are relevant:

- [ ] **S1: No credentials in code** — Secrets never committed to version control
- [ ] **S2: Least privilege by default** — Permissions minimally scoped
- [ ] **O1: Ownership declared** — Data stores/APIs have declared owners
- [ ] **O2: Boundaries respected** — No direct cross-boundary data modification
- [ ] **T1: State changes traceable** — Mutations traceable to request/user/event
- [ ] **T2: Decisions logged** — Security/business decisions recorded with context
- [ ] **SF1: Rollback possible** — Production changes have rollback procedure
- [ ] **SF2: Fail-safe defaults** — Systems fail closed, not open

*See "Starter Invariants" in Principle 4 of AOSD_FRAMEWORK.md for full rationale.*

### Domain-Specific Invariants

List additional invariants specific to your application/domain:

- [Your domain invariant 1]
- [Your domain invariant 2]

Example domain invariants (replace with your own):
- Multi-Tenant DDB Pattern
- AWS Factory Pattern
- PII never in logs
- All API calls idempotent

This section tells Claude Code what rules must not be violated.

---

# 4a. Security-by-Design Checklist

Review security considerations for this slice:

- [ ] **Authentication**: Are auth boundaries maintained?
- [ ] **Authorization**: Is tenant isolation preserved?
- [ ] **Input Validation**: Are all inputs validated at boundaries?
- [ ] **Data Protection**: Is sensitive data handled securely?
- [ ] **Audit Trail**: Are security-relevant actions logged?
- [ ] **Fail-Safe Defaults**: Does the slice fail securely?

If any security item requires special handling, document here:
> [Security notes]

---

# 4b. Scaling Considerations

Consider team and coordination implications:

- **Team Impact**: [Solo / Small team / Multi-team coordination needed]
- **Parallel Work Risk**: Could this conflict with other in-progress slices? [Yes/No]
- **Knowledge Sharing**: Does this introduce patterns others need to learn? [Yes/No]
- **Documentation Debt**: What docs will need updating after completion?

If this slice affects multiple developers or teams:
> [Coordination notes]

---

# 4c. Restrictive Environment Considerations

If this slice may be deployed in restricted environments:

- [ ] **Air-Gap Safe**: Works without external network access
- [ ] **Compliance Ready**: Meets ITAR/export control requirements
- [ ] **Local AI Only**: Can function with on-premises AI models
- [ ] **N/A**: Not applicable to restrictive environments

If environment restrictions apply:
> [Environment notes]

---

# 5. Technical Changes (High-Level)

List expected changes:

- modified files
- new modules or functions
- data model changes (if any)
- prompt changes
- handler updates
- UI changes
- new tests
- new Makefile targets (if needed)

This is not a detailed designjust enough to guide TodoWrite.

---

# 6. Inputs & Outputs

Briefly list:

### Inputs:
- API requests
- parameters
- table items
- events

### Outputs:
- updated items
- new versions
- returned payloads
- prompt blocks
- logs/metrics

---

# 7. Tests Required

### Fast Tests (pure functions)
- FT1:
- FT2:

### Medium Tests (mocked AWS)
- MT1:
- MT2:

### Slow Tests (integration)
- IT1:
- IT2:

### Playwright Tests (Jim's workflow)
- PT1:
- PT2:

---

# 8. Deployment Notes

- Requires `ENV=dev make deploy`?
- Requires migration?
- Requires updating any Infra stacks?

If no special deployment rules, write:
> Standard Dev deploy.

---

# 9. Failure Modes

Consider how this slice can fail and what mitigations exist.

### Blast Radius

- **Scope**: [Small / Medium / Large]
- What components/services are affected if this slice fails?
- Is the failure isolated or does it cascade?
- What data could be corrupted or lost?

### Failure Scenarios

Document potential failure modes:

- FM1: [Scenario] → [Impact] → [Detection]
- FM2: [Scenario] → [Impact] → [Detection]

*Common scenarios to consider: downstream dependency failures, data corruption, timeouts, resource exhaustion, invalid state transitions.*

### Compensating Actions

- What automatic recovery exists? (retries, circuit breakers)
- What manual intervention may be required?

### Rollback Strategy

- How do we revert this change?
- Is rollback automatic, manual, or requires coordination?
- What data migration considerations exist?
- Estimated rollback time: [instant / minutes / hours / complex]

*Links to Safety Invariants: SF1 (Rollback possible) and SF2 (Fail-safe defaults) from Starter Invariants.*

### When to Skip

If this slice has minimal blast radius (e.g., documentation-only, UI-only with no state), write:
> Minimal blast radius — no failure mode analysis required.

---

# 10. Open Questions

Any unknowns needing clarification:

- OQ1:
- OQ2:

---

# 11. Training Integration

If this slice affects user experience or introduces new features:

- [ ] **Inline Help Needed**: Does the feature need contextual explanations?
- [ ] **Guided Workflow**: Should AI walk users through this feature?
- [ ] **Error Prevention**: Should AI proactively warn about common mistakes?
- [ ] **Documentation**: What user-facing docs need updating?
- [ ] **N/A**: No training integration needed

Training notes:
> [How will users learn about this feature?]

---

# 12. Cross-Artifact Sync (from META_GUIDE.md)

After completing this slice, what else needs updating?

- [ ] ADRs to create/update
- [ ] MEMORY patterns to update
- [ ] Framework docs affected
- [ ] Template updates needed
- [ ] Reference implementations to update
- [ ] None - self-contained slice

*Per META_GUIDE.md: No update is complete unless all dependent artifacts are updated.*

---

# 13. Evidence Package Requirements

Based on the risk tier for this slice, what evidence must be produced?

### Risk Tier: [High / Medium / Low / Zero]

*Reference: See "Evidence Packages" in AOSD_FRAMEWORK.md (Principle 6) for full guidance.*

### Required Evidence (for High/Medium risk):
- [ ] Slice ID documented
- [ ] Risk tier recorded
- [ ] Tests executed with proof (CI links/artifacts)
- [ ] Security scans completed with proof
- [ ] Invariants validated
- [ ] Rollback procedure referenced

### Optional Evidence:
- [ ] Approver recorded
- [ ] Deployment target documented
- [ ] Related ADRs linked

### Storage Location:
> [Specify where evidence package will be stored per Level 2 Orchestration Profile]

---

# 14. TodoWrite Seed (Optional)

Provide a seed list Claude Code must expand:

```
* [ ] Analyze slice spec
* [ ] Verify security-by-design checklist items
* [ ] Identify impacted modules
* [ ] Update handler
* [ ] Add tests (Fast/Medium/Slow tiers)
* [ ] Validate invariants compliance
* [ ] Update docs
* [ ] Deploy to Dev
* [ ] Run Playwright tests (Jim workflow)
* [ ] Complete cross-artifact sync items
* [ ] Produce evidence package (per risk tier)
```

---

**End of SLICE_SPEC_TEMPLATE.md**
