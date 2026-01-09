# Documentation Governance

**AOSD Framework - Multi-Level Documentation Management**

This document defines governance rules for documentation across AOSD's three-level architecture, ensuring consistency, clear ownership, and proper change management.

*See also: [META_GUIDE.md](./META_GUIDE.md) for maintaining the AOSD Framework (Level 1) itself.*

---

## 1. Purpose

AOSD documentation spans three levels, each with different ownership, update frequency, and governance needs:

- **Level 1 (Framework)**: Universal methodology maintained by framework authors
- **Level 2 (Orchestration Profile)**: Organization-specific implementation
- **Level 3 (Application)**: Project-specific documentation

This guide ensures:
- Clear responsibility for each documentation level
- Proper version compatibility management
- Well-defined override and customization rules
- Consistent update workflows

---

## 2. Level Responsibilities

### 2.1 Level 1: Framework (This Repository)

**Ownership**: AOSD Framework maintainers

**Scope**:
- Core principles and goals
- Universal patterns and templates
- Training curriculum
- Reference implementations

**Governance Rules**:
| Rule | Description |
|------|-------------|
| **Stability** | Changes must be backward-compatible when possible |
| **Versioning** | Semantic versioning (MAJOR.MINOR.PATCH) |
| **Review** | All changes require maintainer review |
| **Cascade** | Changes must cascade to dependent artifacts (see META_GUIDE.md) |

**Change Authority**: Framework maintainers only

---

### 2.2 Level 2: Orchestration Profile

**Ownership**: Organization or individual practitioner

**Scope**:
- Platform-specific implementations (AWS, Azure, GCP, etc.)
- AI agent configurations
- Organization-specific patterns
- Tool and workflow choices

**Governance Rules**:
| Rule | Description |
|------|-------------|
| **Compatibility** | Must declare compatible L1 version |
| **Override documentation** | All deviations from L1 must be documented |
| **Versioning** | Recommended: semantic versioning |
| **Updates** | Should align with L1 updates periodically |

**Change Authority**: Profile owner(s)

**Compatibility Declaration** (required in L2 profile):
```markdown
## Framework Compatibility
**AOSD Framework Version**: 0.x.x
**Tested With**: [specific version]
**Last Compatibility Check**: [YYYY-MM-DD]
```

---

### 2.3 Level 3: Application

**Ownership**: Application team / project owner

**Scope**:
- Application-specific invariants
- Project-specific patterns
- Slice specifications
- Architecture Decision Records (ADRs)
- Context Nuggets

**Governance Rules**:
| Rule | Description |
|------|-------------|
| **Inheritance** | Must reference L2 profile |
| **Override documentation** | Deviations from L2 must be documented |
| **Living documentation** | Should be updated with each significant change |
| **AI-readable** | Documentation must support AI agent consumption |

**Change Authority**: Application team

---

## 3. Version Compatibility

### 3.1 Version Compatibility Matrix

| L1 Change Type | L2 Impact | L3 Impact | Action Required |
|----------------|-----------|-----------|-----------------|
| **PATCH** (0.1.x) | Compatible | Compatible | Optional update |
| **MINOR** (0.x.0) | Review needed | Likely compatible | Check new features |
| **MAJOR** (x.0.0) | Update required | Update L2 first | Migration path |

### 3.2 Compatibility Checking

**L2 profiles should declare**:
```markdown
## Framework Compatibility
**AOSD Framework Version**: 0.1.x (compatible with 0.1.0 - 0.1.99)
**Breaking Changes Awareness**: [List any L1 features explicitly not adopted]
```

**L3 applications should declare**:
```markdown
## Orchestration Profile
**Profile**: [Link to L2]
**Profile Version**: [commit hash or version]
**Framework Version**: [inherited from L2]
```

### 3.3 Compatibility Drift Detection

Signs your documentation is drifting from the framework:
- L1 terminology doesn't match L2/L3 usage
- L2 patterns conflict with current L1 guidance
- L3 invariants contradict L2 constraints
- Cross-references point to renamed/removed sections

---

## 4. Override Rules

### 4.1 When Overrides Are Allowed

| Level | Can Override | Requirements |
|-------|--------------|--------------|
| **L2 → L1** | Patterns only (not principles) | Must document rationale |
| **L3 → L2** | Patterns and configurations | Must document rationale |
| **L3 → L1** | Never directly (override via L2) | Create L2 override first |

### 4.2 Override Documentation Format

When L2 overrides an L1 pattern:
```markdown
## Override: [Pattern Name]

**L1 Default**: [What the framework specifies]
**L2 Override**: [What this profile does instead]
**Rationale**: [Why this deviation is necessary]
**Risk Assessment**: [What could go wrong]
**Review Cadence**: [How often to reconsider this override]
```

When L3 overrides an L2 configuration:
```markdown
## Override: [Configuration Name]

**L2 Default**: [What the profile specifies]
**L3 Override**: [What this application does]
**Rationale**: [Why this application needs different behavior]
**Approved By**: [Human who approved the override]
**Expiration**: [When to revisit this decision]
```

### 4.3 Prohibited Overrides

The following CANNOT be overridden at any level:
- Core AOSD principles (1-12)
- Trustworthy-by-Design requirements
- Human-in-the-Loop oversight for critical operations
- Environment isolation requirements
- Security invariants

---

## 5. Consistency Check Slice

A **Consistency Check Slice** is a special slice type dedicated to verifying documentation alignment across levels.

### 5.1 When to Run Consistency Checks

| Trigger | Urgency | Scope |
|---------|---------|-------|
| **L1 version update** | High | Full check all levels |
| **Quarterly review** | Medium | Spot check critical sections |
| **New team member onboarding** | Low | Verify current state |
| **After major L3 changes** | Medium | L3 ↔ L2 alignment |
| **Drift symptoms detected** | High | Targeted investigation |

### 5.2 Consistency Check Steps

1. **Version alignment**: Verify L2 declares compatible L1 version
2. **Terminology check**: Ensure consistent term usage across levels
3. **Cross-reference validation**: Verify all links resolve
4. **Override audit**: Confirm all overrides are documented
5. **Pattern alignment**: Check L2/L3 patterns match L1 guidance
6. **Invariant consistency**: Verify invariants don't conflict

### 5.3 Drift Report Output

Consistency checks should produce a drift report:

```markdown
# Documentation Drift Report

**Date**: YYYY-MM-DD
**Scope**: [L1/L2/L3 checked]
**Checker**: [Human/AI agent name]

## Summary
- **Issues Found**: [count]
- **Critical**: [count]
- **Warnings**: [count]

## Critical Issues
1. **[Issue]**: [Description]
   - **Location**: [File:line]
   - **Expected**: [What should be there]
   - **Actual**: [What was found]
   - **Fix**: [Recommended action]

## Warnings
1. **[Warning]**: [Description]
   - **Location**: [File:line]
   - **Recommendation**: [Suggested improvement]

## Alignment Confirmed
- [List items verified as correctly aligned]
```

*See [TEMPLATES/CONSISTENCY_CHECK_SLICE.md](../TEMPLATES/CONSISTENCY_CHECK_SLICE.md) for the slice specification template.*

---

## 6. AI Agent Behavior for Documentation

### 6.1 Safe Documentation-Check Behavior

When AI agents perform documentation checks, they must:

**DO**:
- Report discrepancies without automatically fixing
- Ask human for approval before making changes
- Provide specific file:line references
- Suggest fixes but await confirmation
- Log all findings for review

**DON'T**:
- Automatically update documentation
- Make assumptions about intended behavior
- Skip reporting minor discrepancies
- Fix L1 documentation (human-only)
- Modify cross-level overrides without approval

### 6.2 Agent Prompt for Documentation Checks

```
You are performing a documentation consistency check.

Rules:
  * Report all discrepancies you find
  * DO NOT automatically fix issues
  * Provide file:line references for each issue
  * Categorize as Critical, Warning, or Info
  * Suggest fixes but await human approval
  * If unsure about intent, ask for clarification

Scope:
  * Level: [L1/L2/L3 or all]
  * Focus: [terminology/cross-references/overrides/all]

Output: Generate a drift report following the template format.
```

---

## 7. Update Workflows

### 7.1 L1 Update Workflow (Framework Maintainers)

```
1. Propose change (issue/discussion)
2. Review dependencies (META_GUIDE.md)
3. Implement with all cascading updates
4. Version bump
5. CHANGELOG update
6. PR review
7. Merge
8. Announce to practitioners
```

### 7.2 L2 Update Workflow (Profile Owners)

```
1. Check L1 compatibility
2. Plan change and overrides
3. Document any deviations
4. Update version compatibility declaration
5. Test with sample L3 application
6. Commit and publish
```

### 7.3 L3 Update Workflow (Application Teams)

```
1. Check L2 profile version
2. Make documentation changes
3. Document any L2 overrides
4. Update last-modified date
5. Run consistency check (recommended)
6. Commit with clear message
```

---

## 8. Cross-References

- **META_GUIDE.md**: Maintaining Level 1 specifically
- **AOSD_FRAMEWORK.md**: Source of truth for principles
- **IMPLEMENTATION_TEMPLATE.md**: Creating Level 2 profiles
- **APP_TEMPLATE.md**: Creating Level 3 documentation
- **TEMPLATES/CONSISTENCY_CHECK_SLICE.md**: Consistency check slice template

---

**End of DOCUMENTATION_GOVERNANCE.md**
