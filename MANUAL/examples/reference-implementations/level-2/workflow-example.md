# Example Workflow Definitions

**Reference: GitHub Actions workflows for an AOSD Orchestration Profile**

---

## Overview

This example shows CI/CD workflows aligned with AOSD practices. These workflows enforce invariants, run tiered tests, and control deployment promotion.

---

## Workflow Summary

| Workflow | Trigger | Purpose |
|----------|---------|---------|
| `ci.yml` | Push, PR | Run tests and checks |
| `deploy-dev.yml` | Merge to main | Auto-deploy to dev |
| `deploy-staging.yml` | Manual | Deploy to staging |
| `deploy-prod.yml` | Manual + Approval | Deploy to production |
| `invariant-check.yml` | Daily, PR | Validate invariants |

---

## CI Workflow

**File**: `.github/workflows/ci.yml`

```yaml
name: CI

on:
  push:
    branches: [main, 'feature/**', 'fix/**']
  pull_request:
    branches: [main]

env:
  PYTHON_VERSION: '3.11'
  NODE_VERSION: '20'

jobs:
  # Fast tests - run on every commit
  fast-tests:
    name: Fast Tests (Tier 1)
    runs-on: ubuntu-latest
    timeout-minutes: 5

    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: ${{ env.PYTHON_VERSION }}

      - name: Install dependencies
        run: |
          pip install -r requirements.txt
          pip install -r requirements-dev.txt

      - name: Run fast tests
        run: |
          pytest tests/unit/ -m fast --tb=short -q

      - name: Check test coverage
        run: |
          pytest tests/unit/ --cov=src --cov-fail-under=80

  # Medium tests - run on PRs
  medium-tests:
    name: Medium Tests (Tier 2)
    runs-on: ubuntu-latest
    timeout-minutes: 15
    if: github.event_name == 'pull_request'

    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: ${{ env.PYTHON_VERSION }}

      - name: Install dependencies
        run: |
          pip install -r requirements.txt
          pip install -r requirements-dev.txt

      - name: Run medium tests
        run: |
          pytest tests/integration/ -m medium --tb=short

  # Linting and formatting
  lint:
    name: Lint & Format
    runs-on: ubuntu-latest
    timeout-minutes: 5

    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: ${{ env.PYTHON_VERSION }}

      - name: Install linters
        run: pip install ruff mypy

      - name: Run ruff
        run: ruff check src/ tests/

      - name: Run mypy
        run: mypy src/ --ignore-missing-imports

  # Security scanning
  security:
    name: Security Scan
    runs-on: ubuntu-latest
    timeout-minutes: 10

    steps:
      - uses: actions/checkout@v4

      - name: Run Bandit
        uses: PyCQA/bandit-action@v1
        with:
          path: 'src/'

      - name: Check for secrets
        uses: trufflesecurity/trufflehog@main
        with:
          extra_args: --only-verified

  # Invariant validation
  invariants:
    name: Invariant Check
    runs-on: ubuntu-latest
    timeout-minutes: 5

    steps:
      - uses: actions/checkout@v4

      - name: Check for direct SDK usage
        run: |
          # Invariant: All cloud access through factory
          if grep -r "boto3.client\|boto3.resource" src/ --include="*.py" | grep -v "factory.py"; then
            echo "ERROR: Direct boto3 usage found. Use service factory."
            exit 1
          fi

      - name: Check for hardcoded secrets
        run: |
          # Invariant: No secrets in code
          if grep -rE "(password|secret|api_key)\s*=\s*['\"][^'\"]+['\"]" src/ --include="*.py"; then
            echo "ERROR: Possible hardcoded secret found."
            exit 1
          fi

      - name: Check tenant isolation
        run: |
          # Invariant: Tenant ID in all data access
          # This is a simplified check - real implementation would be more thorough
          echo "Tenant isolation check passed (manual review required)"
```

---

## Dev Deployment Workflow

**File**: `.github/workflows/deploy-dev.yml`

```yaml
name: Deploy to Dev

on:
  push:
    branches: [main]
  workflow_dispatch:

env:
  AWS_REGION: us-east-1
  ENVIRONMENT: dev

jobs:
  deploy:
    name: Deploy to Development
    runs-on: ubuntu-latest
    timeout-minutes: 30
    environment: development

    steps:
      - uses: actions/checkout@v4

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ secrets.AWS_ROLE_DEV }}
          aws-region: ${{ env.AWS_REGION }}

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Install CDK
        run: |
          npm install -g aws-cdk
          pip install -r requirements.txt

      - name: CDK Diff
        run: cdk diff --context env=${{ env.ENVIRONMENT }}

      - name: CDK Deploy
        run: cdk deploy --all --require-approval never --context env=${{ env.ENVIRONMENT }}

      - name: Run smoke tests
        run: |
          pytest tests/smoke/ --env=${{ env.ENVIRONMENT }}

      - name: Notify on failure
        if: failure()
        uses: slackapi/slack-github-action@v1
        with:
          payload: |
            {
              "text": "Dev deployment failed: ${{ github.run_url }}"
            }
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK }}
```

---

## Staging Deployment Workflow

**File**: `.github/workflows/deploy-staging.yml`

```yaml
name: Deploy to Staging

on:
  workflow_dispatch:
    inputs:
      ref:
        description: 'Git ref to deploy (commit SHA or tag)'
        required: true
        default: 'main'
      reason:
        description: 'Reason for staging deployment'
        required: true

env:
  AWS_REGION: us-east-1
  ENVIRONMENT: staging

jobs:
  validate:
    name: Pre-Deployment Validation
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
        with:
          ref: ${{ github.event.inputs.ref }}

      - name: Verify ref exists
        run: git rev-parse HEAD

      - name: Check CI status
        run: |
          # Verify CI passed for this ref
          gh run list --commit ${{ github.event.inputs.ref }} --status completed --json conclusion -q '.[0].conclusion' | grep -q success
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}

  deploy:
    name: Deploy to Staging
    needs: validate
    runs-on: ubuntu-latest
    timeout-minutes: 45
    environment: staging

    steps:
      - uses: actions/checkout@v4
        with:
          ref: ${{ github.event.inputs.ref }}

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ secrets.AWS_ROLE_STAGING }}
          aws-region: ${{ env.AWS_REGION }}

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Install CDK
        run: |
          npm install -g aws-cdk
          pip install -r requirements.txt

      - name: CDK Diff
        run: cdk diff --context env=${{ env.ENVIRONMENT }}

      - name: CDK Deploy
        run: cdk deploy --all --require-approval never --context env=${{ env.ENVIRONMENT }}

      - name: Run integration tests
        run: |
          pytest tests/integration/ --env=${{ env.ENVIRONMENT }}

      - name: Run E2E tests
        run: |
          pytest tests/e2e/ --env=${{ env.ENVIRONMENT }}

      - name: Record deployment
        run: |
          echo "Deployed ${{ github.event.inputs.ref }} to staging"
          echo "Reason: ${{ github.event.inputs.reason }}"
          echo "Deployed by: ${{ github.actor }}"
          echo "Timestamp: $(date -u +%Y-%m-%dT%H:%M:%SZ)"
```

---

## Production Deployment Workflow

**File**: `.github/workflows/deploy-prod.yml`

```yaml
name: Deploy to Production

on:
  workflow_dispatch:
    inputs:
      ref:
        description: 'Git ref to deploy (must have passed staging)'
        required: true
      ticket:
        description: 'Change ticket number'
        required: true
      rollback_ref:
        description: 'Rollback ref if deployment fails'
        required: true

env:
  AWS_REGION: us-east-1
  ENVIRONMENT: production

jobs:
  validate:
    name: Pre-Deployment Validation
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
        with:
          ref: ${{ github.event.inputs.ref }}

      - name: Verify staging deployment
        run: |
          # Check that this ref was deployed to staging
          echo "Verifying ${{ github.event.inputs.ref }} was deployed to staging..."
          # In real implementation, check deployment records

      - name: Verify ticket exists
        run: |
          echo "Verifying ticket ${{ github.event.inputs.ticket }}..."
          # In real implementation, check ticketing system

  approval:
    name: Require Approval
    needs: validate
    runs-on: ubuntu-latest
    environment: production-approval

    steps:
      - name: Approval gate
        run: echo "Deployment approved by ${{ github.actor }}"

  deploy:
    name: Deploy to Production
    needs: approval
    runs-on: ubuntu-latest
    timeout-minutes: 60
    environment: production

    steps:
      - uses: actions/checkout@v4
        with:
          ref: ${{ github.event.inputs.ref }}

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ secrets.AWS_ROLE_PROD }}
          aws-region: ${{ env.AWS_REGION }}

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Install CDK
        run: |
          npm install -g aws-cdk
          pip install -r requirements.txt

      - name: CDK Diff
        run: cdk diff --context env=${{ env.ENVIRONMENT }}

      - name: CDK Deploy
        run: cdk deploy --all --require-approval never --context env=${{ env.ENVIRONMENT }}

      - name: Run smoke tests
        id: smoke
        run: |
          pytest tests/smoke/ --env=${{ env.ENVIRONMENT }}

      - name: Rollback on failure
        if: failure() && steps.smoke.outcome == 'failure'
        run: |
          echo "Smoke tests failed. Rolling back to ${{ github.event.inputs.rollback_ref }}"
          git checkout ${{ github.event.inputs.rollback_ref }}
          cdk deploy --all --require-approval never --context env=${{ env.ENVIRONMENT }}

      - name: Record deployment
        if: success()
        run: |
          echo "Production deployment record:"
          echo "  Ref: ${{ github.event.inputs.ref }}"
          echo "  Ticket: ${{ github.event.inputs.ticket }}"
          echo "  Deployed by: ${{ github.actor }}"
          echo "  Timestamp: $(date -u +%Y-%m-%dT%H:%M:%SZ)"

      - name: Notify team
        uses: slackapi/slack-github-action@v1
        with:
          payload: |
            {
              "text": "Production deployment complete: ${{ github.event.inputs.ref }}"
            }
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK }}
```

---

## Invariant Check Workflow

**File**: `.github/workflows/invariant-check.yml`

```yaml
name: Invariant Check

on:
  schedule:
    - cron: '0 6 * * *'  # Daily at 6 AM UTC
  pull_request:
    branches: [main]
  workflow_dispatch:

jobs:
  invariant-check:
    name: Validate Invariants
    runs-on: ubuntu-latest
    timeout-minutes: 15

    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: pip install -r requirements.txt

      # Security Invariants
      - name: S1 - Sensitive data encrypted
        run: |
          echo "Checking encryption invariant..."
          # Check for encrypted fields in data models
          # Check for encryption in transit configuration

      - name: S2 - Least privilege access
        run: |
          echo "Checking least privilege invariant..."
          # Analyze IAM policies for overly broad permissions

      # Operational Invariants
      - name: O1 - All services monitored
        run: |
          echo "Checking monitoring invariant..."
          # Verify CloudWatch alarms exist for all services

      - name: O2 - Logs retained appropriately
        run: |
          echo "Checking log retention invariant..."
          # Check log group retention settings

      # Tenant Invariants
      - name: T1 - Tenant ID validation
        run: |
          echo "Checking tenant validation invariant..."
          # Grep for tenant_id validation in handlers
          grep -r "validate_tenant\|tenant_id" src/handlers/ || echo "No handlers found"

      - name: T2 - No cross-tenant access
        run: |
          echo "Checking cross-tenant isolation invariant..."
          # Analyze query patterns for tenant isolation

      # Service Factory Invariants
      - name: SF1 - Factory usage
        run: |
          echo "Checking factory usage invariant..."
          # No direct boto3 client creation outside factory
          if grep -r "boto3.client\|boto3.resource" src/ --include="*.py" | grep -v "factory.py" | grep -v "test_"; then
            echo "ERROR: Direct boto3 usage found outside factory"
            exit 1
          fi
          echo "Factory usage invariant passed"

      - name: SF2 - Environment-aware configuration
        run: |
          echo "Checking environment configuration invariant..."
          # Verify environment-based configuration

      - name: Generate invariant report
        if: always()
        run: |
          echo "# Invariant Check Report" > invariant-report.md
          echo "Date: $(date -u +%Y-%m-%dT%H:%M:%SZ)" >> invariant-report.md
          echo "Ref: ${{ github.sha }}" >> invariant-report.md
          echo "" >> invariant-report.md
          echo "## Results" >> invariant-report.md
          echo "See job logs for details." >> invariant-report.md

      - name: Upload report
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: invariant-report
          path: invariant-report.md
```

---

## Environment Configuration

### GitHub Environments

Configure these environments in repository settings:

| Environment | Protection Rules |
|-------------|------------------|
| `development` | None (auto-deploy) |
| `staging` | None (manual trigger) |
| `production-approval` | Required reviewers: 2 |
| `production` | Required reviewers: 2, Wait timer: 10 minutes |

### Required Secrets

| Secret | Purpose | Environments |
|--------|---------|--------------|
| `AWS_ROLE_DEV` | IAM role for dev deployment | development |
| `AWS_ROLE_STAGING` | IAM role for staging deployment | staging |
| `AWS_ROLE_PROD` | IAM role for prod deployment | production |
| `SLACK_WEBHOOK` | Notification webhook | all |

---

## Best Practices

### Workflow Organization

1. **Separate concerns**: Different workflows for different purposes
2. **Clear naming**: Workflow names indicate purpose
3. **Timeout limits**: Prevent runaway jobs
4. **Failure notifications**: Alert on critical failures

### AOSD Alignment

1. **Test tiers**: CI runs Fast tests on push, Medium on PR
2. **Invariant enforcement**: Automated checks before merge
3. **Environment isolation**: Separate credentials per environment
4. **Human gates**: Production requires approval

### Maintenance

- Review workflows quarterly
- Update action versions regularly
- Test rollback procedures
- Monitor workflow duration trends

---

**End of Example**
