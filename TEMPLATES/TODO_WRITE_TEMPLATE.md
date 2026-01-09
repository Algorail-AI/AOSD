# TodoWrite Plan Template

<!--
AI INGESTION GUIDELINES:
- This TodoWrite template ensures systematic, trackable AI-assisted development
- AI agents MUST assess risk tier before proceeding (Section 0)
- Cross-artifact sync section (Section 10) ensures documentation consistency
- See AI_GUIDE.md for TodoWrite workflow patterns
- See META_GUIDE.md for propagation rules
-->

---

## Work Item Reference

| Field | Value |
|-------|-------|
| **Work Item ID** | #[issue-number] |
| **Branch Name** | `feature/[number]-[description]` |
| **Slice Spec** | [Link to slice spec if applicable] |
| **Created** | [YYYY-MM-DD] |
| **Status** | Draft / Approved / In Progress / Complete |

*Link this TodoWrite plan to the work item that tracks it.*

---

**AOSD Development  Standard TodoWrite Format**
**For use by AI agents when planning implementation work**

---

## Purpose

TodoWrite is the planning step AI agents must perform *before* writing or modifying code that carries risk (see Risk Tiers in Section 0 below and AOSD_FRAMEWORK.md Principle 6).

This ensures:

- predictable work sequencing
- safe, reviewable plans
- alignment with AOSD invariants
- easier debugging and iteration
- clear rollback or cancellation points

AI agents must generate a TodoWrite plan **exactly in this format** for every slice or feature.

---

# TodoWrite Plan Template

Below is the canonical structure.
Claude should fill out each section using the spec provided by ChatGPT or the human.

```
# TodoWrite Plan

## Work Item: #[issue-number]
**Branch**: feature/[number]-[description]
**Slice Spec**: [Link if applicable]

## 0. Risk Assessment

**Risk Tier:** [High / Medium / Low / Zero]

### Risk Tier Definitions

| Tier | Risk Level | Examples | HITL Requirement | TodoWrite |
|------|------------|----------|------------------|-----------|
| **High** | Security, data, production | IAM policies, DB migrations, auth changes, prod config | Full review required | Mandatory |
| **Medium** | Business logic, integrations | API endpoints, service calls, handlers | Spot check | Recommended |
| **Low** | UI, tests, documentation | Component styling, unit tests, comments | Automated only | Optional |
| **Zero** | Generated, trivial | Boilerplate, imports, formatting, typos | None | Skip |

**Rationale for this tier:**
- [ ] Touches invariants or security boundaries? (→ High)
- [ ] Multi-file or architectural change? (→ High)
- [ ] Code logic, handlers, or business rules? (→ Medium)
- [ ] Just docs or comments? (→ Low)
- [ ] Just typos or formatting? (→ Zero)

**TodoWrite Required?** [Mandatory / Recommended / Optional / Skip]

*See MANUAL/AOSD_FRAMEWORK.md Principle 6 for full risk tier guidance.*

**Git & Branching:**
- **Base branch**: [main / feature/integration-branch]
- **Feature branch**: [feature/{issue}-{description} / fix/{issue}-{description} / docs/{scope} / refactor/{scope}]
- **Branch type**: [Simple feature / Integration branch / Urgent fix / Refactor / Documentation]
- **Sync frequency**: [N/A / Daily / Every 3 days] (for long-running branches)
- **Feature flag**: [None / {flag_name}] (if incremental delivery)

*See [Version Control and Branching](../MANUAL/AOSD_FRAMEWORK.md#10-version-control-and-branching) in the framework for branching strategy.*

---

## 1. Understand the Task

* [ ] Summarize slice or feature in own words
* [ ] Identify primary goals
* [ ] Identify non-goals
* [ ] Identify all AOSD invariants involved (DDB, AWS Factory, WAF, Bundling, etc.)
* [ ] Identify files likely to be affected

## 2. Design & Data Flow

* [ ] Describe expected data flow
* [ ] Describe control flow changes
* [ ] Identify potential architectural risks
* [ ] Identify any needed UI, API, or prompt builder changes
* [ ] Identify environment-specific rules (Dev only; never Prod)

## 3. Implementation Tasks

* [ ] Create or update modules
* [ ] Modify handlers / business logic
* [ ] Add new functions
* [ ] Update prompt builders (if applicable)
* [ ] Update data model (if needed)
* [ ] Maintain all AOSD invariants
* [ ] Follow multi-tenant DDB rules
* [ ] Use AWS Factory for all AWS calls
* [ ] Follow Lambda Bundling Pattern
* [ ] Maintain Decision System constraints (if applicable)
* [ ] Keep changes minimal for constrained environment tasks

## 3a. Security-by-Design Tasks

* [ ] Verify authentication boundaries maintained
* [ ] Confirm authorization/tenant isolation
* [ ] Validate all inputs at boundaries
* [ ] Check sensitive data handling
* [ ] Ensure audit logging for security events
* [ ] Verify fail-safe defaults
* [ ] Run security scan before commit

## 3b. Scaling & Team Considerations

* [ ] Coordinate with parallel work (if applicable)
* [ ] Document any new patterns for team awareness
* [ ] Consider impact on other developers' work
* [ ] Plan knowledge transfer (if introducing new concepts)

## 3c. Restrictive Environment Adaptation (if applicable)

* [ ] Verify works in network-isolated environments
* [ ] Confirm compliance requirements met
* [ ] Test with local/constrained AI (if needed)
* [ ] N/A - standard environment only

## 4. Testing Tasks

### Fast Tests (pure functions)

* [ ] Add/modify pure-function tests
* [ ] Ensure deterministic behavior

### Medium Tests (mocked AWS)

* [ ] Add/modify mocked AWS tests
* [ ] Use moto/stubs through AWS Factory

### Slow Tests (integration)

* [ ] Add/modify integration tests

### Playwright (Jim's workflow)

* [ ] Identify any new E2E flows for laptop-run Playwright testing

## 5. Documentation Tasks

* [ ] Update in-repo docs (README sections, slice docs)
* [ ] Add or update pattern documentation references (if needed)
* [ ] Update architectural notes if this slice affects invariants
* [ ] Update changelog or release notes

## 5a. Training Integration Tasks

* [ ] Add inline help for new features (if user-facing)
* [ ] Update guided workflows (if applicable)
* [ ] Implement error prevention guidance (if applicable)
* [ ] Update user documentation
* [ ] N/A - no training integration needed

## 6. Deployment Tasks

* [ ] Dev deployment: `ENV=dev make deploy`
* [ ] Validate logs and metrics
* [ ] (If needed) Staging deployment steps (future)
* [ ] NEVER deploy to Prod without explicit human direction

## 7. Validation & UAT

* [ ] Manual validation on Dev environment
* [ ] Run Playwright tests (Jim workflow)
* [ ] Perform sanity checks across UI + API

## 8. Completion Criteria

* [ ] All tests pass (fast, medium, slow)
* [ ] Code follows AOSD invariants
* [ ] All documentation updated
* [ ] Human UAT approved
* [ ] Ready for PR creation

## 9. PR Preparation

* [ ] Summarize changes clearly
* [ ] Note any architectural risks
* [ ] Link to slice spec
* [ ] Reference work item in PR (Fixes #[issue-number])
* [ ] Attach test results
* [ ] Prepare for ChatGPT review

## 10. Cross-Artifact Sync (from META_GUIDE.md)

*Per META_GUIDE.md: No update is complete unless all dependent artifacts are updated.*

**Documentation Updates**:
* [ ] AOSD_FRAMEWORK.md updates needed? [Yes/No]
* [ ] AI_GUIDE.md quick references affected? [Yes/No]
* [ ] SYLLABUS modules impacted? [Yes/No]

**Template Updates**:
* [ ] Templates need updating? [Yes/No]
* [ ] Review checklist additions? [Yes/No]

**Reference Updates**:
* [ ] Reference implementations affected? [Yes/No]
* [ ] Pattern library additions? [Yes/No]

**Version Impact**:
* [ ] Framework version bump needed? [None/PATCH/MINOR/MAJOR]

## 11. Work Item Closure

* [ ] All acceptance criteria met
* [ ] Cross-artifact sync completed
* [ ] PR merged to main
* [ ] Work item closed (auto-close via PR or manual)
* [ ] Branch deleted

# End of TodoWrite Plan
```

---

## Usage Notes for AI Agents

**Builder/Implementer agents** must:

- Assess risk tier first (section 0 of template)
- For **High-risk** changes: Always produce full TodoWrite plan before touching code
- For **Medium-risk** changes: Produce TodoWrite unless user explicitly waives it
- For **Low-risk** changes: TodoWrite optional; proceed with awareness
- For **Zero-risk** changes: Skip TodoWrite; just fix it
- When TodoWrite is used:
  - Use the entire template, even if some sections are short
  - Treat TodoWrite as a contract with the user
  - Allow the user to confirm, modify, or reject the plan
  - Only execute changes after the TodoWrite plan is marked as approved

**Reviewer/Architect agents** may:

- Review TodoWrite plans
- Suggest improvements
- Identify missing invariants or risks

Other agents (assistants, code generators, autocomplete tools) should **not** generate or modify TodoWrite plans unless explicitly instructed.

---

**End of TODO_WRITE_TEMPLATE.md**
