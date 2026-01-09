# AWS + Claude Development Workflow Reference Implementation

**AOSD Level 2 Example - AWS Platform with Claude Code and ChatGPT**

> **Note**: This is a reference implementation showing how AOSD development workflow principles can be implemented using AWS infrastructure with Claude Code as the primary builder and ChatGPT as the architect/reviewer. Adapt these patterns for your specific platform and AI toolset.

---

## 1. Purpose

This document defines an **AI-native development workflow** implemented on AWS using:

- Claude Code (primary builder)
- ChatGPT (architect + reviewer)
- Codex (optional builder/refactorer)
- Git-based workflows on Dev EC2 and Prod EC2
- Multi-environment deployment patterns (Dev to Staging to Prod)

This workflow makes AI-driven development **reliable, safe, repeatable, and secure**.

---

## 2. Roles in the Workflow

### 2.1 Human Roles

- **Owner / Architect**
  Defines requirements, approves designs, evaluates output.

- **Operator**
  Runs commands on Dev EC2 and Prod EC2, validates builds, executes deployments.

### 2.2 AI Roles

- **Claude Code - Builder / Implementer**
  Generates code, performs refactors, creates tests, updates docs.

- **ChatGPT - Architect / Reviewer**
  Reviews Claude's work, finds edge cases, identifies architectural risks, suggests improvements.

- **Codex - Optional Builder**
  Used when fast code synthesis or structured refactoring is needed.

---

## 3. High-Level Workflow Summary

1. **Define scope** - Slice Spec or Issue
2. **Ask ChatGPT for architectural review** (optional but recommended)
3. **Hand off to Claude Code**
4. **TodoWrite plan**
5. **Implement + fast tests**
6. **Deploy to Dev**
7. **Playwright tests from laptop**
8. **UAT in Dev environment**
9. **Merge to main**
10. **Deploy to Staging (future)**
11. **Full Playwright suite**
12. **Tag release and deploy to Prod**
13. **Post-release validation**

This workflow scales whether you are solo or have multiple AI agents working in parallel.

---

## 4. Step-by-Step Development Workflow

### Step 1 - Define Scope (Slice Spec / Issue)

Every feature begins with a **clear unit of work**, either:

- a Slice Spec
- a GitHub Issue
- an architectural change record

A good Slice Spec includes:

- Background
- Requirements
- Inputs/Outputs
- Architecture constraints
- Tests & acceptance criteria
- Deployment impact
- Security considerations
- Any required invariants

---

### Step 2 - ChatGPT Architectural Review (Recommended)

Before coding:

- Ask ChatGPT to verify the design
- Ask for edge cases, data-model implications, security risks
- Ask whether the work should be broken into smaller slices
- Ask whether the work touches environment rules or invariants

This step dramatically reduces rework later.

---

### Step 3 - Handoff to Claude Code

Paste the spec into Claude Code with:

> "Implement the following slice using TodoWrite, following all AOSD architecture invariants and environment rules."

Claude must:

- Read the spec
- Infer patterns
- Prepare for TodoWrite

---

### Step 4 - TodoWrite Planning

Claude Code must produce a **TodoWrite plan BEFORE any code changes**, including:

1. Code tasks
2. Test tasks
3. Documentation updates
4. Infra changes (CDK, DynamoDB, WAF, etc.)
5. Deployment steps
6. UAT steps
7. Any environment-specific requirements (`ENV=dev` only)
8. Security checks (linting, test suite, bundling checks)

TodoWrite ensures Claude understands the full scope.

---

### Step 5 - Implementation (Dev EC2)

Claude executes the TodoWrite tasks **one at a time**, marking:

- `in_progress`
- `completed`

During implementation:

- Run fast tests regularly:
```
make test-fast
```
- Maintain strict adherence to:
  - AWS Factory Pattern
  - Lambda Bundling Pattern
  - WAF Pattern
  - Decision Catalog Pattern
  - Multi-tenant DynamoDB pattern

---

### Step 6 - Testing

#### 6.1 Fast Tests
Used during active development.

```
make test-fast
```

#### 6.2 Medium Tests
Before pushing:

```
make test-pre-push
```

#### 6.3 Full Test Suite
Before merge or prod deployment:

```
make test
```

---

### Step 7 - Deploy to Dev Environment

From Dev EC2:

```
ENV=dev make deploy
```

Then validate:

- Lambda invocation works
- API Gateway routes resolve
- WAF rules still intact
- CloudWatch logs show no errors
- DynamoDB writes behave correctly

---

### Step 8 - Playwright Testing (Laptop)

From your laptop:

```
npx playwright test --config=playwright.dev.config.ts
```

Dev tests may be **full lifecycle**, including destructive operations.

---

### Step 9 - Dev UAT

You validate:

- UI behavior
- Workflow correctness
- Control/document generation quality
- Decision catalog interactions
- No regressions

Once UAT passes, ready for PR.

---

### Step 10 - Create PR and Merge to Main

Claude Code can run:

```
/pr
```

Or manually:

1. Push feature branch
2. Create PR
3. CI runs:
   - Lint
   - Fast/medium tests
   - Full test suite
   - Security scanning
   - Deployment synthesis

Only merge after CI + review.

---

### Step 11 - Deploy to Staging (Future)

When Staging exists, deploy from Prod EC2:

```
ENV=stg make deploy
```

Then:

- Full Playwright suite (via laptop)
- Manual review in Staging
- Validate environment parity with Prod

---

### Step 12 - Prod Deployment

Performed **exclusively** on Prod EC2:

#### Pre-flight checks:
```
ENV=prod make test
ENV=prod make security-pre-deploy
```

#### Deploy:
```
ENV=prod make deploy
```

#### Post-deploy smoke tests:
```
npx playwright test --config=playwright.prod.config.ts --grep @smoke
```

Prod tests must be **non-destructive**.

---

### Step 13 - Post-Release Validation

- Check logs
- Confirm metrics (latency, WAF, throttles)
- Validate error rates
- Validate document generation correctness

Document findings in:
- Release notes
- Architecture log
- Risk register

---

## 5. Parallel Workflows (Optional)

Claude Code can run multiple sessions using Git worktrees:

- Session 1 = Backend feature
- Session 2 = Frontend feature

Rules:

- No overlapping files
- No conflicting migrations
- Deployment rights remain with primary session

This is an **advanced** practice and should only be used once the basic workflow is stable.

---

## 6. Constrained Environment Workflow Reference

When working inside constrained environments with limited AI capabilities:

- Only limited AI models may be available
- Context window is smaller
- Multi-file reasoning is limited
- Tasks must be broken into **atomic units**
- Refactors must be minimized
- Architectural work should be done in less-constrained environment

See: [Restrictive Environment Patterns](../restrictive-environments/README.md)

---

## 7. AWS-Specific Development Rules

- TodoWrite for every feature
- No raw boto3 calls (use AWS Factory)
- No WAF disablement
- No local code (laptop)
- No destructive Prod tests
- Dev to (Staging) to Prod promotion required
- All code generated on Dev EC2
- SSM-only access to EC2
- Claude Code follows invariants exactly
- ChatGPT reviews critical architecture decisions

---

## 8. Summary

This workflow operationalizes AI-native engineering using AOSD on AWS.
It ensures that:

- AI remains predictable
- Architecture stays consistent
- Deployments remain safe
- Environments stay isolated
- Code quality stays high
- Security is never compromised

---

## Related Documents

- [AOSD Framework](../../../AOSD_FRAMEWORK.md) - Core methodology
- [Development Workflow Principles](../../../DEVELOPMENT_WORKFLOW.md) - Generalized workflow
- [AWS Environment Strategy](../aws/environment-strategy-aws.md) - AWS environment details
- [Restrictive Environment Patterns](../restrictive-environments/README.md) - Constrained environment patterns

---

**End of AWS + Claude Development Workflow Reference Implementation**
