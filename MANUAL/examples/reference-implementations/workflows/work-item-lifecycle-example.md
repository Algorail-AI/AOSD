# Work Item Lifecycle Reference Implementation

**AOSD Level 2 Example - GitHub Issues Workflow**

> **Note**: This is a reference implementation showing how the AOSD Work Item Lifecycle principle (Principle 11) can be implemented using GitHub Issues as the work tracking platform. Adapt these patterns for your specific platform (Jira, Azure DevOps, Linear, etc.).

---

## 1. Purpose

This document demonstrates how to implement the **Work Item Lifecycle** principle with:

- GitHub Issues as the work tracking platform
- Feature branches linked to issues
- Commit messages referencing work items
- PR-based review and merge workflow
- Automation for lifecycle stage transitions

This workflow ensures **traceability, accountability, and systematic delivery** of all development work.

---

## 2. Lifecycle Stages Overview

```
┌─────────────┐     ┌──────────────┐     ┌─────────────────┐
│   CREATED   │────▶│   ANALYSIS   │────▶│ BRANCH CREATED  │
└─────────────┘     └──────────────┘     └─────────────────┘
                                                  │
                                                  ▼
┌─────────────┐     ┌──────────────┐     ┌─────────────────┐
│   CLOSED    │◀────│    MERGED    │◀────│ IMPLEMENTATION  │
└─────────────┘     └──────────────┘     └─────────────────┘
                           ▲
                           │
                    ┌──────────────┐
                    │    REVIEW    │
                    └──────────────┘
```

---

## 3. Work Item Creation

### 3.1 Issue Template Structure

Every work item (GitHub Issue) should include:

```markdown
## Summary
[1-2 sentence description of what needs to be done]

## Context
[Background information and why this work is needed]

## Tasks
- [ ] Task 1
- [ ] Task 2
- [ ] Task 3

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2

## Dependencies
- Blocked by: #XX (if applicable)
- Blocks: #YY (if applicable)

## Files to Update
- `path/to/file1.ts`
- `path/to/file2.ts`
```

### 3.2 Labels for Stage Tracking

Use labels to track lifecycle stages:

| Label | Stage | Description |
|-------|-------|-------------|
| `status:new` | Created | Newly created, not yet analyzed |
| `status:analysis` | Analysis | Requirements being clarified |
| `status:ready` | Ready | Ready for implementation |
| `status:in-progress` | Implementation | Work has started |
| `status:review` | Review | PR created, under review |
| `status:blocked` | Any | Blocked by external dependency |

---

## 4. Branch Naming Conventions

### 4.1 Standard Pattern

```
<type>/<issue-number>-<short-description>
```

### 4.2 Branch Types

| Type | Use Case | Example |
|------|----------|---------|
| `feature/` | New functionality | `feature/42-add-user-auth` |
| `fix/` | Bug fixes | `fix/87-resolve-timeout` |
| `docs/` | Documentation only | `docs/103-update-readme` |
| `refactor/` | Code restructuring | `refactor/156-extract-utils` |
| `test/` | Test additions/fixes | `test/201-add-api-tests` |

### 4.3 Examples

```bash
# Feature implementation
git checkout -b feature/42-add-user-authentication

# Bug fix
git checkout -b fix/87-resolve-connection-timeout

# Documentation update
git checkout -b docs/103-update-api-documentation
```

### 4.4 Branch Creation Command

```bash
# Standard branch creation from issue number
ISSUE=42
DESCRIPTION="add-user-auth"
git checkout -b "feature/${ISSUE}-${DESCRIPTION}"
```

---

## 5. Commit Message Format

### 5.1 Standard Format

```
<type>(<scope>): <subject>

<body>

Refs #<issue-number>
```

### 5.2 Commit Types

| Type | Description |
|------|-------------|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation |
| `style` | Formatting (no code change) |
| `refactor` | Code restructuring |
| `test` | Adding tests |
| `chore` | Maintenance tasks |

### 5.3 Examples

```bash
# Feature commit
git commit -m "feat(auth): add JWT token validation

Implement token validation middleware for API routes.
Includes refresh token handling.

Refs #42"

# Bug fix commit
git commit -m "fix(api): resolve connection timeout on large requests

Increase timeout to 30s for file upload endpoints.

Refs #87"

# Documentation commit
git commit -m "docs(api): update authentication endpoints

Add examples for OAuth flow.

Refs #103"
```

### 5.4 Multi-Issue Reference

When a commit addresses multiple issues:

```bash
git commit -m "feat(auth): implement SSO integration

Add SAML-based SSO for enterprise customers.

Refs #42, #43, #44"
```

---

## 6. Pull Request Workflow

### 6.1 PR Title Format

```
[#<issue>] <type>: <description>
```

Examples:
- `[#42] feat: Add user authentication`
- `[#87] fix: Resolve connection timeout`
- `[#103] docs: Update API documentation`

### 6.2 PR Description Template

```markdown
## Summary
[Brief description of changes]

## Related Issue
Closes #42

## Changes Made
- Change 1
- Change 2
- Change 3

## Test Plan
- [ ] Unit tests added/updated
- [ ] Integration tests pass
- [ ] Manual testing completed

## Screenshots
[If applicable]

## Checklist
- [ ] Code follows project style guidelines
- [ ] Self-review completed
- [ ] Documentation updated
- [ ] Tests pass locally
```

### 6.3 Auto-Closing Issues

Use GitHub keywords to automatically close issues when PR merges:

```markdown
Closes #42
Fixes #87
Resolves #103
```

---

## 7. Complete Lifecycle Example

### Step 1: Issue Created

```markdown
# Issue #47: Update reference examples for Work Item Lifecycle

## Summary
Update reference implementations to demonstrate the Work Item Lifecycle principle.

## Tasks
- [ ] Review existing examples
- [ ] Add work item → branch → commit → PR → merge flow examples
- [ ] Demonstrate branch naming conventions
- [ ] Show commit message formats

## Acceptance Criteria
- [ ] New reference implementation document created
- [ ] README files updated
- [ ] Examples are platform-agnostic where possible
```

Label: `status:new`

### Step 2: Analysis Complete

After reviewing requirements and identifying affected files:

Label changed to: `status:ready`

### Step 3: Branch Created

```bash
git checkout -b feature/47-work-item-lifecycle-examples
```

Label changed to: `status:in-progress`

### Step 4: Implementation with Commits

```bash
# First commit - add main example file
git commit -m "docs(examples): add work item lifecycle reference

Create comprehensive example showing GitHub Issues workflow
with branch naming, commit formats, and PR patterns.

Refs #47"

# Second commit - update READMEs
git commit -m "docs(examples): update READMEs for lifecycle example

Add work item lifecycle to workflows README and main
reference implementations README.

Refs #47"
```

### Step 5: PR Created

```bash
gh pr create --title "[#47] docs: Add work item lifecycle reference implementation" \
  --body "## Summary
Add reference implementation for Work Item Lifecycle principle.

## Related Issue
Closes #47

## Changes Made
- New work-item-lifecycle-example.md
- Updated workflows/README.md
- Updated reference-implementations/README.md

## Test Plan
- [x] Documentation reviewed for accuracy
- [x] Links verified
- [x] Format consistent with other examples"
```

Label changed to: `status:review`

### Step 6: Review and Merge

After approval:
- PR merged to main
- Issue #47 automatically closed
- Label removed (issue closed)

---

## 8. Automation Options

### 8.1 GitHub Actions for Label Management

```yaml
# .github/workflows/issue-lifecycle.yml
name: Issue Lifecycle

on:
  issues:
    types: [opened, labeled]
  pull_request:
    types: [opened, closed]

jobs:
  update-labels:
    runs-on: ubuntu-latest
    steps:
      - name: Add new label on issue creation
        if: github.event_name == 'issues' && github.event.action == 'opened'
        uses: actions/github-script@v6
        with:
          script: |
            await github.rest.issues.addLabels({
              owner: context.repo.owner,
              repo: context.repo.repo,
              issue_number: context.issue.number,
              labels: ['status:new']
            })

      - name: Update label when PR opened
        if: github.event_name == 'pull_request' && github.event.action == 'opened'
        uses: actions/github-script@v6
        with:
          script: |
            // Extract issue number from PR body
            const body = context.payload.pull_request.body || '';
            const match = body.match(/(?:closes|fixes|resolves)\s+#(\d+)/i);
            if (match) {
              const issueNumber = parseInt(match[1]);
              await github.rest.issues.removeLabel({
                owner: context.repo.owner,
                repo: context.repo.repo,
                issue_number: issueNumber,
                name: 'status:in-progress'
              }).catch(() => {});
              await github.rest.issues.addLabels({
                owner: context.repo.owner,
                repo: context.repo.repo,
                issue_number: issueNumber,
                labels: ['status:review']
              });
            }
```

### 8.2 Branch Protection Rules

Configure branch protection to enforce the lifecycle:

- Require PR reviews before merging
- Require status checks to pass
- Require branches to be up to date
- Require linear history (optional)

### 8.3 Commit Message Linting

Use commitlint to enforce message format:

```json
// commitlint.config.js
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'references-empty': [2, 'never'],
    'footer-max-line-length': [0, 'always']
  }
};
```

---

## 9. Platform Adaptations

### 9.1 Jira

| GitHub Concept | Jira Equivalent |
|----------------|-----------------|
| Issue number | Issue key (e.g., `PROJ-42`) |
| Branch: `feature/42-desc` | Branch: `feature/PROJ-42-desc` |
| Commit: `Refs #42` | Commit: `PROJ-42` (auto-linked) |
| Labels | Status workflow |

### 9.2 Azure DevOps

| GitHub Concept | Azure DevOps Equivalent |
|----------------|-------------------------|
| Issue number | Work Item ID |
| Branch: `feature/42-desc` | Branch: `feature/42-desc` |
| Commit: `Refs #42` | Commit: `#42` or `AB#42` |
| Labels | Work Item State |

### 9.3 Linear

| GitHub Concept | Linear Equivalent |
|----------------|-------------------|
| Issue number | Issue ID (e.g., `ENG-42`) |
| Branch: `feature/42-desc` | Branch: `eng-42-desc` (auto-generated) |
| Commit: `Refs #42` | Commit includes `ENG-42` |
| Labels | Status + Labels |

---

## 10. Benefits of This Workflow

### Traceability
- Every code change links to a work item
- Full history from request to completion
- Easy to understand why changes were made

### Context Preservation
- AI agents can reference work item for context
- Branch names immediately indicate purpose
- Commit messages explain the "why"

### Progress Visibility
- Labels show current stage
- Stakeholders see work status without asking
- Automation keeps status current

### Quality Gates
- Each stage can have validation requirements
- PRs enforce review before merge
- Automation prevents skipping steps

---

## Related Documents

- [AOSD Framework - Principle 11](../../../AOSD_FRAMEWORK.md#11-work-item-lifecycle) - Core principle definition
- [Development Workflow Principles](../../../DEVELOPMENT_WORKFLOW.md) - Generalized workflow
- [AWS + Claude Workflow](./aws-claude-workflow.md) - Complete development workflow example
- [Slice Spec Template](../../../../TEMPLATES/SLICE_SPEC_TEMPLATE.md) - Feature specification template

---

**End of Work Item Lifecycle Reference Implementation**
