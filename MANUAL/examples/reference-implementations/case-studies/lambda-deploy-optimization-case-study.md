# Case Study: Lambda Deploy Optimization

**AOSD Level 2 Example - Measurement Before Refactoring**

> **Note**: This case study documents a real AOSD session. It demonstrates the "measure before refactor" principle and multi-agent disagreement resolution. This is an example, not doctrine.

---

## Context

A team noticed their CDK deployments felt slow. The CDK stack was large (3,205 LOC) and the Makefile was complex. An issue was opened proposing to split the monolithic stack into multiple smaller stacks to improve deploy speed and maintainability.

Three AI agents were involved: Claude Code (implementation), ChatGPT (architecture review), and Gemini (second opinion).

---

## Initial Hypothesis

**Assumption**: The monolithic CDK stack structure was causing slow deployments. Splitting into multiple stacks would:
- Reduce deployment time by enabling parallel deployments
- Improve maintainability through smaller, focused stacks
- Reduce blast radius of changes

This seemed reasonable. Large files often correlate with problems.

---

## The Challenge

The Human Orchestrator questioned the premise:
- Would stack splitting actually reduce deploy time?
- What was the risk of resource migrations in CloudFormation?
- Was this worth the effort for a pre-revenue system?

Rather than debating opinions, the team measured where time was actually going.

---

## Measurement Results

Instrumentation revealed:

| Phase | Time |
|-------|------|
| CDK synth + asset bundling | ~2 minutes |
| CloudFormation execution | ~13-18 minutes |

The bottleneck was not stack structure. Deeper investigation found:

- 8 Lambda functions, each bundling ~70MB of dependencies
- boto3/botocore included in every bundle (~30MB each)
- Total upload per deploy: ~552MB, mostly duplicated dependencies
- AWS Lambda runtime already provides boto3—bundling it was unnecessary

**The original hypothesis was wrong.** Stack splitting would not reduce asset size, bundling time, or deploy duration. It would introduce migration risk while solving the wrong problem.

---

## Solution Pivot

The actual fix:

1. **Exclude boto3/botocore from bundles** — runtime provides them
2. **Introduce shared Lambda Layer** — heavy stable dependencies bundled once
3. **Standardize on ARM64** — better price/performance
4. **Refactor into CDK Constructs** — improve maintainability without splitting stacks

This addressed the real bottleneck without the risk of stack splitting.

---

## Governance Outcome

The original issue was converted to a **decision record** documenting:
- What was proposed
- What was measured
- Why the approach changed

A new issue was created for **execution** of the actual fix.

This separation preserves institutional memory and prevents re-litigation.

---

## AOSD Principles Demonstrated

| Principle | Application |
|-----------|-------------|
| Measure before refactor | Data invalidated the original hypothesis |
| Agent disagreement → measurement | Claude, ChatGPT, and Gemini had different views; measurement resolved it |
| Smells are hypotheses | Large stack size was correlated with pain, not causal |
| Decision/execution separation | Original issue became decision record; new issue for work |
| Risk-aware sequencing | No late-night implementation; work queued for next day |

---

## Key Takeaway

Structural complexity often correlates with problems but rarely causes them directly. When proposing performance-motivated refactors, measure the dominant bottleneck first. The fix is usually simpler and less risky than the refactor.

---

**End of Case Study**
