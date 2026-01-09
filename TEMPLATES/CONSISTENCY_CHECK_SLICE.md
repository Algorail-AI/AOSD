# Consistency Check Slice Template

**AOSD Framework - Documentation Consistency Verification**

---

> **Instructions**: Use this template to create a slice specification for documentation consistency checks. Delete this instruction block when done.

---

# Slice: Documentation Consistency Check

**Slice ID**: `CONSISTENCY-[YYYY-MM-DD]`
**Date**: [YYYY-MM-DD]
**Orchestrator**: [Name]
**Agent(s)**: [AI agents involved, if any]

---

## 1. Trigger

**Why is this check being run?**

Select one:
- [ ] L1 Framework version update (version X.Y.Z released)
- [ ] Quarterly scheduled review
- [ ] New team member onboarding
- [ ] Post-major L3 changes
- [ ] Drift symptoms detected: [describe symptoms]
- [ ] Other: [describe reason]

---

## 2. Scope

**What levels are being checked?**

| Level | Included | Location |
|-------|----------|----------|
| L1 (Framework) | [ ] Yes / [ ] No | `[AOSD repo URL or path]` |
| L2 (Profile) | [ ] Yes / [ ] No | `[Profile repo URL or path]` |
| L3 (Application) | [ ] Yes / [ ] No | `[App repo AOSD/ folder path]` |

**Focus areas** (select all that apply):
- [ ] Version compatibility declarations
- [ ] Terminology consistency
- [ ] Cross-reference validation
- [ ] Override documentation
- [ ] Pattern alignment
- [ ] Invariant consistency
- [ ] Template alignment
- [ ] Full check (all areas)

---

## 3. Checklist

### 3.1 Version Alignment

- [ ] L2 profile declares L1 version compatibility
- [ ] L2 compatibility range is valid for current L1 version
- [ ] L3 references specific L2 version/commit
- [ ] Any version mismatches documented

**Findings**:
```
[Record version alignment findings here]
```

### 3.2 Terminology Check

- [ ] "Orchestrator" used consistently (not "developer" in AOSD context)
- [ ] "Slice" used consistently (not "feature" or "task")
- [ ] "Invariant" used consistently (not "rule" or "constraint")
- [ ] Principle/Goal numbering matches L1

**Findings**:
```
[Record terminology findings here]
```

### 3.3 Cross-Reference Validation

- [ ] All markdown links resolve
- [ ] Section references point to existing sections
- [ ] File paths are correct
- [ ] External links are valid (if checked)

**Broken References Found**:
```
[List broken references with file:line]
```

### 3.4 Override Audit

- [ ] All L2 → L1 overrides documented
- [ ] All L3 → L2 overrides documented
- [ ] Override rationales provided
- [ ] No prohibited overrides present

**Override Issues**:
```
[List override documentation gaps]
```

### 3.5 Pattern Alignment

- [ ] L2 patterns align with current L1 patterns
- [ ] L3 patterns align with L2 profile
- [ ] Deprecated patterns not in use
- [ ] New L1 patterns adopted where applicable

**Pattern Misalignments**:
```
[List pattern alignment issues]
```

### 3.6 Invariant Consistency

- [ ] L2 invariants don't contradict L1 principles
- [ ] L3 invariants don't contradict L2 constraints
- [ ] Security invariants properly inherited
- [ ] Environment isolation maintained

**Invariant Conflicts**:
```
[List invariant conflicts]
```

---

## 4. Drift Report

### Summary

| Category | Count |
|----------|-------|
| **Critical Issues** | [#] |
| **Warnings** | [#] |
| **Info/Minor** | [#] |
| **Items Verified OK** | [#] |

### Critical Issues

Issues that must be fixed immediately:

| ID | Issue | Location | Expected | Actual | Fix |
|----|-------|----------|----------|--------|-----|
| C1 | [description] | [file:line] | [expected] | [found] | [action] |
| C2 | | | | | |

### Warnings

Issues that should be addressed:

| ID | Issue | Location | Recommendation |
|----|-------|----------|----------------|
| W1 | [description] | [file:line] | [recommended action] |
| W2 | | | |

### Info/Minor

Observations for consideration:

| ID | Observation | Location | Note |
|----|-------------|----------|------|
| I1 | [description] | [file:line] | [optional context] |
| I2 | | | |

---

## 5. Remediation Plan

### Immediate Actions (Critical)

| Issue ID | Owner | Due Date | Status |
|----------|-------|----------|--------|
| C1 | [name] | [date] | [ ] Not started / [ ] In progress / [ ] Done |
| C2 | | | |

### Short-term Actions (Warnings)

| Issue ID | Owner | Due Date | Status |
|----------|-------|----------|--------|
| W1 | [name] | [date] | [ ] Not started / [ ] In progress / [ ] Done |
| W2 | | | |

### Deferred Actions (Info)

| Issue ID | Decision | Notes |
|----------|----------|-------|
| I1 | [ ] Will fix / [ ] Won't fix / [ ] Needs discussion | [rationale] |
| I2 | | |

---

## 6. Completion

**Check Completed**: [YYYY-MM-DD HH:MM]
**Completed By**: [name]
**Total Time**: [duration]

**Next Scheduled Check**: [YYYY-MM-DD or "As needed"]

**Sign-off**:
- [ ] All critical issues addressed or tracked
- [ ] Drift report reviewed by orchestrator
- [ ] Remediation plan approved
- [ ] Findings communicated to relevant parties

---

## 7. Notes

[Any additional observations, context, or recommendations for future checks]

---

**End of Consistency Check Slice**
