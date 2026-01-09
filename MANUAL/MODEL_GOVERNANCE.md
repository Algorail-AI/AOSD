# Model Governance & Change Management

**Managing AI Model Versions, Upgrades, and Rollbacks in AOSD**

---

## 1. Purpose

This document defines the governance framework for managing AI model versions within AOSD systems. It covers:

- **Version pinning**: Ensuring reproducible AI behavior
- **Upgrade evaluation**: Systematic assessment before adopting new models
- **Regression detection**: Identifying behavioral degradation
- **Rollback procedures**: Reverting to previous versions safely
- **Environment rules**: Model deployment by environment

**Connection to AOSD Principles**:
- **Principle 2 (Trustworthy-by-Design)**: Predictable, auditable AI behavior
- **Principle 4 (Architectural Invariants)**: Model versions as controlled configuration
- **Pattern 9 (AI Evaluation and Continuous Validation)**: Systematic model assessment

---

## 2. Version Pinning

### 2.1 Why Version Pinning Matters

AI models evolve frequently. Without version pinning:
- Behavior may change unexpectedly between deployments
- Debugging becomes difficult (was it code or model?)
- Cost and performance may fluctuate unpredictably
- Regression testing loses meaning

### 2.2 Version Pinning Requirements

| Environment | Pinning Level | Rationale |
|-------------|---------------|-----------|
| **Production** | Exact version required | `claude-3-5-sonnet-20241022` |
| **Staging** | Exact version required | Must match production for validation |
| **Development** | Exact version recommended | May allow latest for exploration |
| **Testing** | Exact version required | Reproducible test results |

### 2.3 Version Configuration

Store model versions in configuration, not code:

```yaml
# config/models.yaml
models:
  builder:
    provider: anthropic
    model_id: claude-3-5-sonnet-20241022
    max_tokens: 4096
    temperature: 0

  reviewer:
    provider: openai
    model_id: gpt-4-turbo-2024-04-09
    max_tokens: 2048
    temperature: 0

  specialist:
    provider: anthropic
    model_id: claude-3-haiku-20240307
    max_tokens: 1024
    temperature: 0
```

### 2.4 Version Documentation

Maintain a model version registry:

| Model Role | Current Version | Previous Version | Change Date | Evaluation ID |
|------------|-----------------|------------------|-------------|---------------|
| Builder | claude-3-5-sonnet-20241022 | claude-3-opus-20240229 | 2024-11-01 | EVAL_001 |
| Reviewer | gpt-4-turbo-2024-04-09 | gpt-4-1106-preview | 2024-05-15 | EVAL_002 |
| Specialist | claude-3-haiku-20240307 | - | 2024-03-15 | EVAL_003 |

---

## 3. Model Evaluation Slice Pattern

### 3.1 Overview

Model upgrades are treated as slices requiring structured evaluation. Use the [Model Evaluation Template](../TEMPLATES/model_evaluation_template.md) for all assessments.

### 3.2 Evaluation Slice Workflow

```
┌─────────────────────────────────────────────────────────────────┐
│                    Model Evaluation Slice                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  1. Identify      2. Create         3. Run           4. Decide  │
│     New Model →      Evaluation  →     Benchmarks →     Go/No   │
│                      Slice                                      │
│                                                                 │
│  5. Shadow         6. A/B Test      7. Gradual       8. Monitor │
│     Mode (opt) →      (opt)      →     Rollout   →     & Tune   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### 3.3 Creating an Evaluation Slice

```markdown
# Slice: Model Evaluation - [New Model Name]

## Work Item Reference
- **Issue**: #XXX
- **Branch**: `feature/eval-[model-name]`
- **Evaluation ID**: EVAL_[MODEL]_[YYYY-MM-DD]

## Scope
- Evaluate [new model] for [role] use case
- Compare against current model [current version]
- Decide: ADOPT / A/B TEST / SKIP / DEFER

## Acceptance Criteria
- [ ] Benchmark suite executed against new model
- [ ] Quality metrics compared to baseline
- [ ] Cost analysis completed
- [ ] Latency analysis completed
- [ ] Security/safety assessment passed
- [ ] Decision documented in evaluation report
```

### 3.4 Evaluation Dimensions

| Dimension | Metrics | Threshold |
|-----------|---------|-----------|
| **Correctness** | Golden trace pass rate, validation errors | ≥95% pass rate |
| **Performance** | Latency p50/p95/p99, throughput | p95 < 3s |
| **Cost** | Cost per request, monthly projection | ≤150% of current |
| **Safety** | Prompt injection resistance, PII handling | 100% on security tests |
| **Robustness** | Edge case handling, error rates | Error rate < 5% |

---

## 4. Upgrade Evaluation Workflow

### 4.1 Pre-Evaluation Checklist

Before evaluating a new model:

- [ ] New model available in target environment (cloud provider)
- [ ] Benchmark suite up to date
- [ ] Current model baseline metrics documented
- [ ] Evaluation slice created and tracked
- [ ] Human Orchestrator approval to proceed

### 4.2 Benchmark Suite

Maintain a comprehensive benchmark suite:

| Category | Test Type | Purpose |
|----------|-----------|---------|
| **Golden Traces** | Known input/output pairs | Verify expected behavior |
| **Edge Cases** | Boundary conditions | Test robustness |
| **Security** | Prompt injection, jailbreak attempts | Verify safety |
| **Performance** | Load testing, latency measurement | Assess speed/cost |
| **Domain-Specific** | Application-specific scenarios | Verify fit for purpose |

### 4.3 Evaluation Decision Framework

```
Evaluate if:
  (Quality_New ≥ Quality_Current) AND
  (Cost_New ≤ 150% * Cost_Current OR Quality_New > 110% * Quality_Current) AND
  (Latency_New ≤ 120% * Latency_Current) AND
  (Security_Tests_Pass = 100%)

Decision Matrix:
  Quality ↑ AND Cost ↓  → ADOPT
  Quality ↑ AND Cost ↑  → Consider if quality gain justifies cost
  Quality ↓ AND Cost ↓  → Rarely worth the quality loss
  Quality ↓ AND Cost ↑  → SKIP
```

### 4.4 Go/No-Go Checklist

| Criterion | Required | Notes |
|-----------|----------|-------|
| Golden traces pass rate | ≥95% | Hard requirement |
| Quality improved OR cost lower | Yes | Must improve on at least one |
| Migration effort acceptable | <1 day OR ROI >5x | Time/benefit tradeoff |
| No compliance/security concerns | Yes | Hard requirement |
| Latency acceptable | p95 <3s | May vary by use case |

---

## 5. Regression Detection

### 5.1 Regression Types

| Type | Description | Detection Method |
|------|-------------|------------------|
| **Quality Regression** | Worse outputs on known inputs | Golden trace comparison |
| **Performance Regression** | Slower response times | Latency monitoring |
| **Cost Regression** | Higher token usage/cost | Cost tracking |
| **Safety Regression** | Weaker guardrails | Security test suite |
| **Behavioral Drift** | Subtle changes in output patterns | Statistical analysis |

### 5.2 Automated Regression Detection

Integrate regression checks into CI/CD:

```yaml
# .github/workflows/model-regression.yml
name: Model Regression Check

on:
  schedule:
    - cron: '0 6 * * *'  # Daily
  workflow_dispatch:

jobs:
  regression-check:
    runs-on: ubuntu-latest
    steps:
      - name: Run Golden Traces
        run: pytest tests/golden_traces/ --model=${{ env.MODEL_ID }}

      - name: Check Quality Baseline
        run: |
          python scripts/check_quality_baseline.py \
            --model=${{ env.MODEL_ID }} \
            --threshold=0.95

      - name: Alert on Regression
        if: failure()
        run: |
          # Alert Human Orchestrator
          echo "Model regression detected"
```

### 5.3 Regression Response Protocol

| Severity | Detection | Response |
|----------|-----------|----------|
| **Critical** | Security test failure, major quality drop | Immediate rollback |
| **High** | >10% quality regression, >50% cost increase | Rollback within 24h |
| **Medium** | 5-10% quality regression, 20-50% cost increase | Investigate, plan rollback |
| **Low** | <5% quality regression, <20% cost increase | Monitor, document |

### 5.4 Integration with Invariants Cookbook

Use fitness functions from [INVARIANTS_COOKBOOK.md](./INVARIANTS_COOKBOOK.md) for model quality:

```python
# invariant_checks/model_quality.py
def check_model_quality():
    """Verify model meets quality thresholds."""
    results = run_golden_traces()

    if results.pass_rate < 0.95:
        return False, f"Pass rate {results.pass_rate} < 0.95"

    if results.avg_latency > 3000:  # ms
        return False, f"Latency {results.avg_latency}ms > 3000ms"

    return True, "Model quality within bounds"
```

---

## 6. Rollback Procedures

### 6.1 Rollback Triggers

| Trigger | Automatic? | Threshold |
|---------|------------|-----------|
| Security incident | Yes | Any security test failure |
| Quality collapse | Yes | Pass rate < 80% |
| Cost explosion | Yes | Cost > 200% baseline |
| User complaints | No | Human judgment |
| Latency spike | Yes | p95 > 2x baseline |

### 6.2 Rollback Process

**Immediate Rollback (Critical)**:
```
1. Update configuration to previous model version
2. Deploy configuration change
3. Verify rollback successful (check version in logs)
4. Run smoke tests
5. Document incident
6. Post-mortem within 48 hours
```

**Planned Rollback (Non-Critical)**:
```
1. Create rollback issue/slice
2. Update configuration with previous version
3. Deploy through normal pipeline
4. Run regression suite
5. Document decision and rationale
```

### 6.3 Rollback Configuration

Maintain rollback capability:

```yaml
# config/models.yaml
models:
  builder:
    current: claude-3-5-sonnet-20241022
    rollback: claude-3-opus-20240229
    rollback_tested: 2024-10-15

  reviewer:
    current: gpt-4-turbo-2024-04-09
    rollback: gpt-4-1106-preview
    rollback_tested: 2024-04-01
```

### 6.4 Rollback Validation

After any rollback:

- [ ] Verify model version in logs matches rollback version
- [ ] Run critical path smoke tests
- [ ] Confirm cost and latency within expected bounds
- [ ] Monitor for 24 hours
- [ ] Update version registry
- [ ] Create post-mortem if unplanned rollback

---

## 7. Environment-Specific Model Rules

### 7.1 Environment Model Matrix

| Environment | Model Selection | Change Process |
|-------------|-----------------|----------------|
| **Production** | Pinned, validated models only | Full evaluation slice required |
| **Staging** | Must match production | Automatic from production config |
| **Development** | May use newer models for exploration | Document findings, don't assume prod |
| **CI/Testing** | Pinned for reproducibility | Update with production changes |

### 7.2 Model Promotion Path

```
Development (exploration)
       │
       ▼
Evaluation Slice (benchmark)
       │
       ▼
Staging (validation)
       │
       ▼
Production (gradual rollout)
```

### 7.3 Environment Configuration

```yaml
# config/environments/production.yaml
models:
  policy: pinned_only
  allow_upgrade: false  # Requires deploy
  rollback_enabled: true

# config/environments/development.yaml
models:
  policy: exploration_allowed
  allow_upgrade: true  # Can test new models
  rollback_enabled: false  # Not production
```

### 7.4 Environment Guardrails

| Environment | Guardrail | Enforcement |
|-------------|-----------|-------------|
| Production | No unevaluated models | CI/CD blocks deployment |
| Production | Version must be in registry | Configuration validation |
| Staging | Must match production version | Automated sync |
| Development | Must log model version used | Audit logging |

---

## 8. Continuous Model Monitoring

### 8.1 Monitoring Metrics

| Metric | Frequency | Alert Threshold |
|--------|-----------|-----------------|
| Golden trace pass rate | Daily | < 95% |
| Average latency | Hourly | > 120% baseline |
| Cost per request | Hourly | > 150% baseline |
| Error rate | Real-time | > 5% |
| Token usage | Daily | > 120% baseline |

### 8.2 Monitoring Dashboard

Track these metrics for each model in production:

```
┌────────────────────────────────────────────────────────────────┐
│                    Model Health Dashboard                       │
├────────────────────────────────────────────────────────────────┤
│ Model: claude-3-5-sonnet-20241022                              │
│ Role: Builder                                                  │
│ Since: 2024-11-01                                              │
├────────────────────────────────────────────────────────────────┤
│ Quality     │ ████████████████████░░ │ 97%  (baseline: 95%)   │
│ Latency p95 │ ███████████░░░░░░░░░░░ │ 1.2s (baseline: 1.5s)  │
│ Cost/req    │ █████████████░░░░░░░░░ │ $0.02 (baseline: $0.02)│
│ Errors      │ ██░░░░░░░░░░░░░░░░░░░░ │ 1.2% (threshold: 5%)   │
└────────────────────────────────────────────────────────────────┘
```

### 8.3 Drift Detection

Monitor for behavioral drift over time:

| Signal | Detection Method | Response |
|--------|------------------|----------|
| Output length change | Statistical tracking | Investigate cause |
| Tone/style shift | Sample review | Verify acceptable |
| New error patterns | Log analysis | Update tests |
| Cost creep | Trend analysis | Re-evaluate model |

---

## 9. Governance Checklist

### Pre-Upgrade

- [ ] New model identified and available
- [ ] Evaluation slice created
- [ ] Benchmark suite ready
- [ ] Current baseline documented
- [ ] Human Orchestrator aware

### During Evaluation

- [ ] Golden traces executed
- [ ] Quality metrics compared
- [ ] Cost analysis completed
- [ ] Latency analysis completed
- [ ] Security tests passed
- [ ] Decision documented

### Post-Upgrade

- [ ] Version registry updated
- [ ] Rollback configuration maintained
- [ ] Monitoring alerts configured
- [ ] Team notified
- [ ] Documentation updated

### Ongoing

- [ ] Daily regression checks running
- [ ] Metrics within thresholds
- [ ] Rollback capability verified monthly
- [ ] Evaluation template up to date

---

## 10. Related Documents

- [Model Evaluation Template](../TEMPLATES/model_evaluation_template.md) - Template for model assessments
- [AOSD Framework](./AOSD_FRAMEWORK.md) - Pattern 9 (AI Evaluation and Continuous Validation)
- [Invariants Cookbook](./INVARIANTS_COOKBOOK.md) - Fitness functions for quality checks
- [Development Workflow](./DEVELOPMENT_WORKFLOW.md) - Change Classes for model upgrades
- [Agent Communication](./AGENT_COMMUNICATION.md) - Model versioning in communication logs

---

**End of MODEL_GOVERNANCE.md**
