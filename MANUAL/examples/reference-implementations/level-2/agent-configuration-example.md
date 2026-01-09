# Example AI Agent Configuration

**Reference: How to configure AI agents for an Orchestration Profile**

---

## Overview

This example shows how to document AI agent configurations in a Level 2 Orchestration Profile. It demonstrates role assignments, model selection, and agent-specific guidelines.

---

## Agent Role Summary

| AOSD Role | Agent | Model | Primary Use |
|-----------|-------|-------|-------------|
| Builder/Implementer | Claude Code | claude-sonnet-4 | Day-to-day development |
| Architect/Reviewer | ChatGPT | gpt-4 | Design review, architecture |
| Transformer | (Same as Builder) | - | Combined role |
| Constrained | (Not applicable) | - | No restricted environments |

---

## Builder Agent: Claude Code

### Role Definition

The Builder agent is responsible for:
- Implementing features from slice specifications
- Writing and running tests
- Creating pull requests
- Fixing bugs and refactoring code

### Configuration

**Model Selection**:
```
Default: claude-sonnet-4
Escalate to claude-opus-4 for:
  - Architecture decisions affecting multiple services
  - Security-sensitive implementations
  - Complex debugging (> 30 minutes on sonnet)
```

**Access Requirements**:
- Full repository read/write access
- CI/CD trigger permissions
- Development environment access

### Context Loading Strategy

**Always load before starting work**:
1. Application's `AOSD/INVARIANTS.md`
2. Relevant slice specification
3. Recent related commits (last 5)

**Load on demand**:
- Full codebase context (for architectural changes)
- Test coverage reports (when adding tests)
- Dependency information (when updating packages)

### Agent Instructions

```markdown
# Claude Code Builder Instructions

You are the Builder agent for Acme Corp projects following AOSD methodology.

## Before Starting Any Task

1. Read the slice specification or issue description
2. Load invariants from `AOSD/INVARIANTS.md`
3. Identify affected areas of the codebase
4. Use TodoWrite to plan multi-step tasks

## During Implementation

1. Follow existing code patterns
2. Run tests frequently
3. Commit in logical chunks with clear messages
4. Reference issue numbers in commits

## Before Creating a PR

1. Self-review your changes
2. Ensure all tests pass
3. Update documentation if needed
4. Verify no invariant violations

## Non-Negotiable Rules

- NEVER bypass WAF or security controls
- NEVER commit secrets or credentials
- NEVER push directly to main branch
- ALWAYS validate tenant ID in data access
- ALWAYS use the service factory for cloud clients
```

### Custom Commands (if supported)

| Command | Purpose | Example |
|---------|---------|---------|
| `/slice {url}` | Implement from slice spec | `/slice issues/42` |
| `/invariants` | Show current invariants | `/invariants` |
| `/test` | Run relevant tests | `/test auth` |
| `/pr` | Create pull request | `/pr` |

---

## Reviewer Agent: ChatGPT

### Role Definition

The Reviewer agent is responsible for:
- Architecture and design review
- Code review for complex changes
- Security assessment
- Documentation review

### Configuration

**Model Selection**:
```
Default: gpt-4
Use gpt-4-turbo for:
  - Large context requirements (> 100k tokens)
  - Complex multi-file analysis
```

**Access Requirements**:
- Read-only repository access
- Access to architecture documents
- Access to slice specifications

### Review Prompts

**Architecture Review Prompt**:
```markdown
# Architecture Review Request

## Context
I'm working on [feature/change] for an AOSD-managed application.

## Documents to Review
- Slice specification: [link or content]
- Proposed design: [description]
- Related invariants: [list]

## Review Focus
1. Does this design align with AOSD principles?
2. Are there any invariant violations?
3. What are the security implications?
4. Are there simpler alternatives?
5. What test coverage is needed?

## Expected Output
- Approval / Concerns / Rejection
- Specific feedback on design
- Suggested improvements
- Risk assessment (High/Medium/Low)
```

**Code Review Prompt**:
```markdown
# Code Review Request

## Context
Reviewing PR for [feature] implementing [slice reference].

## Changes Summary
[Brief description of what changed]

## Review Checklist
- [ ] Follows AOSD principles
- [ ] No invariant violations
- [ ] Appropriate test coverage
- [ ] No security vulnerabilities
- [ ] Clean, maintainable code
- [ ] Documentation updated

## Specific Questions
[Any specific concerns to address]
```

**Security Review Prompt**:
```markdown
# Security Review Request

## Context
Reviewing security implications of [change/feature].

## Change Description
[What is being changed]

## Security Considerations
1. Authentication: [impact]
2. Authorization: [impact]
3. Data protection: [impact]
4. Input validation: [impact]
5. External dependencies: [impact]

## AOSD Security Invariants
[List relevant invariants]

## Expected Output
- Security assessment (Pass/Conditional/Fail)
- Identified vulnerabilities
- Required mitigations
- Recommended additional controls
```

---

## Context Sharing Between Agents

### Shared Context Document

Create a `shared-context.md` that both agents reference:

```markdown
# Shared Agent Context

## Project Overview
- Application: Acme Widget Service
- Platform: AWS (Lambda, DynamoDB, API Gateway)
- Language: Python 3.11
- Framework: FastAPI (handlers), AWS CDK (infrastructure)

## Architecture Summary
- Serverless architecture
- Multi-tenant with partition key isolation
- RESTful API with WAF protection

## Key Invariants (Quick Reference)
1. All cloud access through service factory
2. Tenant ID validated at API boundary
3. WAF cannot be disabled
4. No secrets in code

## Current Focus
- Sprint: 2024-Q1-S2
- Active slices: AUTH-001, API-002
- Known issues: [link to tracker]
```

### Handoff Protocol

When Builder needs Reviewer input:

```markdown
## Builder → Reviewer Handoff

### What Was Done
[Summary of implementation]

### Design Decisions Made
[Key decisions and rationale]

### Questions for Review
1. [Specific question]
2. [Specific question]

### Files to Focus On
- `src/handlers/auth.py` (new)
- `src/services/user.py` (modified)
- `tests/test_auth.py` (new)
```

When Reviewer returns to Builder:

```markdown
## Reviewer → Builder Handoff

### Review Summary
[Overall assessment]

### Approved
- [What can proceed]

### Required Changes
- [Must-fix items]

### Suggestions
- [Nice-to-have improvements]

### Follow-up Needed
- [Items requiring human decision]
```

---

## Model Selection Heuristics

### When to Use Cheaper/Faster Models

| Task Type | Recommended Model | Rationale |
|-----------|------------------|-----------|
| Code completion | claude-haiku / gpt-3.5 | Speed over capability |
| Simple refactoring | claude-sonnet / gpt-4 | Balance |
| Documentation | claude-haiku | Straightforward writing |
| Test generation | claude-sonnet | Needs code understanding |

### When to Escalate to Powerful Models

| Situation | Escalate To | Rationale |
|-----------|-------------|-----------|
| Architecture design | claude-opus / gpt-4-turbo | Complex reasoning |
| Security review | claude-opus / gpt-4 | High stakes |
| Debugging > 30 min | claude-opus | Fresh perspective |
| Multi-service changes | claude-opus | Broad context |

---

## Monitoring and Adjustment

### Metrics to Track

| Metric | Target | Action if Missed |
|--------|--------|------------------|
| Task success rate | > 90% | Review agent instructions |
| Escalation rate | 5-15% | Adjust model selection |
| Review approval rate | > 80% first try | Improve Builder prompts |
| Average task duration | Decreasing trend | Optimize context loading |

### Quarterly Review

Every quarter, assess:
1. Are model assignments still appropriate?
2. Are costs aligned with budget?
3. Are agents following invariants?
4. What friction points exist?

---

**End of Example**
