# Review Checklist Template

<!--
AI INGESTION GUIDELINES:
- This checklist ensures AOSD compliance in code reviews
- Security-by-Design section validates Goal 2 requirements
- Cross-Artifact Sync section ensures documentation consistency per META_GUIDE.md
- AI agents can use this for automated review assistance
- See AI_GUIDE.md for review patterns
-->

---

## Work Item Reference

| Field | Value |
|-------|-------|
| **Work Item ID** | #[issue-number] |
| **PR Number** | #[pr-number] |
| **Branch Name** | `feature/[number]-[description]` |
| **Slice Spec** | [Link to slice spec] |
| **Reviewer** | [Name] |
| **Review Date** | [YYYY-MM-DD] |

---

**AOSD AI-Native Development  PR & Slice Review Checklist**
**Use for human review and ChatGPT architectural review of every PR or slice**

---

## 1. Summary of the Change

- [ ] The reviewer understands the goal of the slice / PR
- [ ] The solution aligns with the slice specification
- [ ] Non-goals were respected

---

## 2. Architecture & Invariants

Verify the change complies with AOSD architecture invariants:

- [ ] **AWS Factory Pattern** (no raw boto3 calls)
- [ ] **Lambda Bundling Pattern** (correct `/asset-output` structure)
- [ ] **WAF Pattern** (all APIs protected; no regressions)
- [ ] **Multi-Tenant DynamoDB Pattern** (PK/SK correct; client validation enforced)
- [ ] **Decision System Pattern** (if applicable)
- [ ] **Revision Instructions Pattern** (if applicable)
- [ ] **Future Framework Parameters Pattern** (ODVs handled correctly)
- [ ] **Security invariants** (IAM, encryption, no public buckets, etc.)
- [ ] **No architectural drift** or introduction of "novel patterns"
- [ ] **Trustworthy-by-design (NIST SP 800-160)** principles followed

(If any invariant is broken, the PR *must* be revised before merge.)

---

## 3. Code Quality & Structure

- [ ] Code is readable and follows existing style
- [ ] Functionality is implemented in small, well-scoped modules
- [ ] No duplication unless explicitly intentional
- [ ] No oversized functions or handlers
- [ ] Pure logic extracted into pure functions where appropriate
- [ ] Separation of concerns respected (functional core / imperative shell)
- [ ] Error handling is clear and logged correctly
- [ ] Logging uses structured fields and avoids sensitive data

---

## 4. Tests (Fast / Medium / Slow)

### **Fast Tests**
- [ ] Pure function tests added or updated
- [ ] Logic is deterministic
- [ ] High-coverage for core algorithms

### **Medium Tests**
- [ ] Handlers tested with moto/stubs
- [ ] AWS Factory used correctly
- [ ] Business logic thoroughly tested

### **Slow / Integration Tests**
- [ ] Integration tests updated if behavior changed
- [ ] Bundling structure validated (no missing `__init__.py`)
- [ ] Multi-tenant flows tested

### **Playwright Tests (Jim Workflow)**
- [ ] Relevant E2E flows identified
- [ ] Appropriate smoke/regression tests planned

If **any** test tier is missing � PR cannot merge.

---

## 5. Prompt Builders (AI-Related Code)

If the slice touches AI prompt generation:

- [ ] Revision instructions injected correctly (system + user + final reminder)
- [ ] Decision injection follows precedence logic
- [ ] ODV/parameter blocks handled per-framework
- [ ] Token usage reasonable (no unnecessary expansions)
- [ ] No removal of required prompt blocks
- [ ] No security-sensitive information leaked into prompts

---

## 6. Environment & Deployment Rules

- [ ] Change is safe for Dev deployment
- [ ] No accidental Prod logic or shortcuts introduced
- [ ] No deployments triggered in PR
- [ ] Environment-aware code handles Dev vs Prod correctly
- [ ] No modifications that would break Staging (future)

---

## 7. Failure Modes

Verify failure mode analysis is complete and appropriate:

### Blast Radius Assessment
- [ ] Blast radius scope identified (Small / Medium / Large)
- [ ] Affected components/services documented
- [ ] Cascade vs isolated failure considered
- [ ] Data corruption/loss risk assessed

### Failure Scenarios
- [ ] Key failure scenarios documented (FM1, FM2, etc.)
- [ ] Impact and detection method specified for each
- [ ] Common scenarios considered (downstream deps, timeouts, resource exhaustion)

### Recovery & Rollback
- [ ] Compensating actions documented (automatic recovery, manual intervention)
- [ ] Rollback strategy defined
- [ ] Rollback time estimate provided
- [ ] Data migration considerations addressed (if applicable)

### Safety Invariants
- [ ] SF1 (Rollback possible) satisfied
- [ ] SF2 (Fail-safe defaults) satisfied

If minimal blast radius (documentation-only, UI-only with no state):
- [ ] "Minimal blast radius" justification provided

---

## 8. Documentation

- [ ] Slice spec updated (if needed)
- [ ] Related MEMORY cards updated (if patterns changed)
- [ ] Architecture docs updated (if impacted)
- [ ] Inline comments explain non-obvious logic

---

## 9. Security & Compliance (Security-by-Design)

### Core Security Requirements
- [ ] No secrets committed
- [ ] IAM least privilege respected
- [ ] WAF configuration unchanged or improved
- [ ] No public S3 buckets
- [ ] No insecure defaults
- [ ] Logging avoids sensitive data
- [ ] Data isolation preserved

### Security-by-Design Verification (Goal 2)
- [ ] **Authentication**: Auth boundaries maintained across the change
- [ ] **Authorization**: Tenant/user isolation preserved
- [ ] **Input Validation**: All new inputs validated at boundaries
- [ ] **Audit Trail**: Security-relevant actions properly logged
- [ ] **Fail-Safe Defaults**: System fails securely (not open)
- [ ] **Defense in Depth**: Multiple protection layers considered

---

## 10. Performance & Scalability

- [ ] No unnecessary DynamoDB scans
- [ ] Queries use correct PK/SK patterns
- [ ] No hot partitions introduced
- [ ] Lambda timeouts and memory sized reasonably
- [ ] Concurrency impact considered

---

## 11. Constrained Environment Considerations (If Applicable)

If slice involves work in constrained environments:

- [ ] Changes work within environment's AI capability limitations
- [ ] No patterns broken that constrained environments must preserve
- [ ] No operations requiring tools unavailable in target environment

---

## 12. Team Scaling Considerations

If working in a multi-developer environment:

- [ ] **Coordination**: No conflicts with parallel in-progress work
- [ ] **Knowledge Sharing**: New patterns documented for team awareness
- [ ] **Slice Ownership**: Clear ownership boundaries maintained
- [ ] **Branch Management**: Follows team branch conventions
- [ ] **N/A**: Solo developer / no team coordination needed

---

## 13. Restrictive Environment Compliance (if applicable)

If operating in restricted environments:

- [ ] **Air-Gap Safe**: Works without external network access
- [ ] **Compliance**: Meets ITAR/export control requirements
- [ ] **Audit Trail**: AI usage properly documented
- [ ] **Local AI Compatible**: Functions with on-premises models
- [ ] **N/A**: Standard environment only

---

## 14. Cross-Artifact Sync (from META_GUIDE.md)

*Per META_GUIDE.md: No update is complete unless all dependent artifacts are updated.*

### Documentation Impact
- [ ] ADRs created/updated if architectural decisions made
- [ ] MEMORY patterns updated if new patterns introduced
- [ ] User documentation updated if behavior changed
- [ ] API documentation updated if interfaces changed

### Template Impact
- [ ] Templates affected by this change? [Yes/No]
- [ ] If yes, template updates included in PR? [Yes/No/N/A]

### Training Impact
- [ ] Inline help updated for user-facing changes
- [ ] Training materials need updating? [Yes/No]

---

## 15. Evidence Package Verification

*Reference: See "Evidence Packages" in AOSD_FRAMEWORK.md (Principle 6) for full guidance.*

Based on the slice's risk tier, verify evidence package completeness:

### Risk Tier: [High / Medium / Low / Zero]

### For High/Medium Risk Slices:
- [ ] Evidence package produced
- [ ] Slice ID documented in package
- [ ] Risk tier recorded in package
- [ ] Test execution proof included (CI links/artifacts)
- [ ] Security scan proof included
- [ ] Invariants validation documented
- [ ] Rollback procedure referenced
- [ ] Package stored in designated location (per Level 2 profile)

### For Low Risk Slices:
- [ ] Lightweight evidence produced (or skipped per team policy)
- [ ] N/A - Zero risk slice

### AI Provenance (if AI-assisted)

*Reference: See "AI Provenance" in AOSD_FRAMEWORK.md (Principle 6) for full guidance.*

If AI agents assisted with this change, verify provenance metadata:

- [ ] Model name and version recorded
- [ ] Agent role documented (Builder, Reviewer, etc.)
- [ ] Slice ID / work item reference included
- [ ] Provenance included in Evidence Package (for High/Medium risk)
- [ ] N/A - No AI assistance for this change

---

## 16. Final Reviewer Actions

- [ ] Code matches TodoWrite plan
- [ ] All invariants satisfied
- [ ] Security-by-design checklist complete
- [ ] Cross-artifact sync requirements addressed
- [ ] Evidence package complete (per risk tier)
- [ ] Tests pass (CI + local)
- [ ] Manual validation performed (Dev)
- [ ] Reviewer approves architecture & implementation quality

---

**End of REVIEW_CHECKLIST_TEMPLATE.md**
