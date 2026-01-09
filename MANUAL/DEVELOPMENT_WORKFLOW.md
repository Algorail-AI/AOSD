# Development Workflow

**AOSD Framework - AI-Native Development Workflow Principles**

---

## 1. Purpose

This document defines the **canonical AI-native development workflow** principles used across AOSD projects. These principles are designed to make AI-driven development **reliable, safe, repeatable, and secure** regardless of specific cloud platform or AI toolset.

These principles apply to any AI agent combination and any infrastructure setup.

---

## 2. Roles in the Workflow

### 2.1 Human Orchestrator

In AOSD, **all AI agents operate under the direction of a Human Orchestrator**. This is the person who:

- Sets strategic direction and slice boundaries
- Selects and assigns AI agents to roles
- Defines and enforces architectural invariants
- Determines trust zones and autonomy levels
- Approves critical decisions and resolves conflicts
- Maintains final authority over all workflow outcomes

The Human Orchestrator role connects directly to **Principle 1 (Multi-Agent Orchestration)** and **Principle 8 (Human-in-the-Loop Oversight)** in the AOSD Framework.

### 2.2 Practical Human Roles

In day-to-day work, the Human Orchestrator responsibilities are typically fulfilled through these practical roles:

| Role | Responsibilities | Orchestrator Aspect |
|------|------------------|---------------------|
| **Owner / Architect** | Defines requirements, approves designs, evaluates output | Strategic direction, invariant enforcement, approval authority |
| **Operator** | Runs commands, validates builds, executes deployments | Execution oversight, environment control |

In solo development, a single person fulfills both roles. In teams, these may be distributed, but **someone must always be the designated Orchestrator** for any given piece of work.

### 2.3 AI Agent Roles

| Role | Responsibilities | Examples |
|------|------------------|----------|
| **Builder Agent** | Generates code, performs refactors, creates tests, updates docs | Claude Code, Cursor, Codex |
| **Reviewer Agent** | Reviews work, finds edge cases, identifies risks, suggests improvements | ChatGPT, Claude, Gemini |
| **Specialist Agent** | Handles domain-specific tasks (security, infrastructure, etc.) | Various specialized tools |

The specific AI tools assigned to each role depend on your Level 2 Orchestration Profile.

---

## 3. High-Level Workflow Summary

1. **Define scope** - Slice Specification or Work Item
2. **Architectural review** (optional but recommended)
3. **Hand off to Builder Agent**
4. **TodoWrite planning**
5. **Implementation + fast tests**
6. **Deploy to Development**
7. **Integration/E2E tests**
8. **User Acceptance Testing (UAT)**
9. **Merge to main branch**
10. **Deploy to Staging** (if applicable)
11. **Full test suite**
12. **Tag release and deploy to Production**
13. **Post-release validation**

This workflow scales from solo developers to multi-agent parallel development.

---

## 4. Workflow Phases

### Phase 1: Define Scope

Every feature begins with a **clear unit of work**:

- Slice Specification
- Work Item / Issue
- Architecture Decision Record

**A good specification includes:**

- Background and context
- Requirements (functional and non-functional)
- Inputs/Outputs
- Architecture constraints
- Tests & acceptance criteria
- Deployment impact
- Security considerations
- Applicable invariants

---

### Phase 1.1: Decision vs Execution Artifacts

When investigation invalidates or significantly changes an issue's original proposal:

1. **Convert the original issue to a decision record**: Document what was proposed, what was learned, and why the approach changed
2. **Create a new issue for execution**: The actual work plan based on findings

This separation preserves institutional memory (the decision) while keeping execution tracking clean (the new issue). It prevents re-litigation of settled questions and makes the reasoning searchable.

**Example**: An issue proposes "split monolithic stack for faster deploys." Investigation reveals bundling is the actual bottleneck, not stack structure. The original issue becomes a decision record documenting the finding; a new issue tracks the actual fix.

---

### Phase 2: Architectural Review (Recommended)

Before implementation:

- Use a Reviewer Agent to verify the design
- Identify edge cases and data-model implications
- Assess security risks
- Determine if work should be broken into smaller slices
- Check for invariant or environment rule impacts

This step dramatically reduces rework.

---

### Phase 3: Builder Agent Handoff

Provide the specification to the Builder Agent with explicit instructions:

> "Implement the following slice using TodoWrite, following all AOSD architecture invariants and environment rules."

The Builder Agent must:

- Read and understand the specification
- Identify applicable patterns
- Prepare for TodoWrite planning

---

### Phase 4: TodoWrite Planning

**Before any code changes**, the Builder Agent must produce a TodoWrite plan including:

1. Code tasks
2. Test tasks
3. Documentation updates
4. Infrastructure changes
5. Deployment steps
6. UAT steps
7. Environment-specific requirements
8. Security checks

TodoWrite ensures the AI agent understands the full scope before starting.

---

### Phase 5: Implementation

The Builder Agent executes TodoWrite tasks **one at a time**, marking each:

- `in_progress` - currently working
- `completed` - finished

During implementation:

- Run fast tests regularly
- Follow all architecture patterns
- Maintain adherence to invariants
- Commit frequently with meaningful messages

---

### Phase 6: Testing

#### Fast Tests (During Development)
Run continuously during active development. Target: < 1 second per test.

#### Medium Tests (Before Commit/Push)
Run before committing changes. Target: < 10 seconds per test.

#### Full Test Suite (Before Merge/Deploy)
Run before merging to main or deploying to production.

---

### Phase 7: Development Deployment

Deploy to development environment:

- Validate basic functionality
- Check infrastructure integration
- Review logs for errors
- Verify data operations

---

### Phase 8: Integration Testing

Run end-to-end tests against development environment:

- Full lifecycle tests allowed in development
- Destructive operations permitted
- Test complete user workflows

---

### Phase 9: User Acceptance Testing (UAT)

Human validation of:

- UI/UX behavior
- Workflow correctness
- Feature completeness
- No regressions

Once UAT passes, the work is ready for Pull Request.

---

### Phase 10: Pull Request and Merge

Create PR following your team's process:

1. Push feature branch
2. Create Pull Request
3. CI/CD runs:
   - Linting
   - Tests (fast, medium, full)
   - Security scanning
   - Build verification

Only merge after CI passes and review is complete.

---

### Phase 11: Staging Deployment (If Applicable)

Deploy to staging from production deployment environment:

- Run full E2E test suite
- Perform manual review
- Validate environment parity with production

---

### Phase 12: Production Deployment

Performed **only** from the production deployment environment.

#### Pre-flight Checks:
- Run full test suite
- Run security validation

#### Deployment:
- Execute deployment command
- Tag release with semantic version

#### Post-deploy Validation:
- Run smoke tests (non-destructive only)
- Verify critical paths

---

### Phase 13: Post-Release Validation

- Review logs and metrics
- Validate performance (latency, throughput)
- Check error rates
- Verify feature correctness

Document findings in appropriate channels (release notes, architecture log, etc.).

---

## 5. Parallel Workflows

When multiple AI agent sessions work simultaneously:

- Use Git worktrees or separate branches
- Ensure no overlapping file modifications
- Coordinate infrastructure changes
- Designate a primary session for deployments

**This is an advanced practice** - establish the basic workflow first.

*For detailed parallel development guidance, see [Parallel Development Patterns](./PARALLEL_DEVELOPMENT.md).*

---

## 6. AI Session Management

Effective AI session management ensures continuity, prevents context pollution, and maintains traceability across development work.

### 6.1 Session-to-Slice Association

Every AI session must be associated with a specific slice or work item:

| Session Type | Association | Example |
|--------------|-------------|---------|
| **Implementation session** | Single slice | `SLICE-042: Add user authentication` |
| **Review session** | PR or slice under review | `Review PR #123` |
| **Exploration session** | Work item or ADR | `Research caching strategies for #45` |
| **Maintenance session** | Specific scope | `Fix bug #67 in auth module` |

**Why Association Matters**:
- Enables cost attribution per slice
- Maintains context boundaries
- Supports audit trail
- Prevents scope creep

### 6.2 Fork vs Start-Fresh Decision

When continuing work from a previous session, decide:

| Scenario | Decision | Rationale |
|----------|----------|-----------|
| Same slice, same day, context still valid | **Fork** (continue session) | Preserve context, avoid re-explaining |
| Same slice, days later | **Start fresh** | Context may be stale, re-load current state |
| Same slice, significant changes since | **Start fresh** | Previous context may mislead |
| Different slice | **Start fresh** | Prevent context pollution |
| Context size > 50% of limit | **Start fresh** | Leave room for new work |
| After merge conflicts or rebasing | **Start fresh** | Git state has changed significantly |

**Fork Protocol**:
1. Load previous session context (handoff notes, memory files)
2. Verify assumptions are still valid
3. Resume from documented state
4. Update handoff notes at session end

**Start-Fresh Protocol**:
1. Begin with clean context
2. Load slice spec and relevant documentation
3. Review recent commits if continuing work
4. Do not carry over assumptions from previous sessions

### 6.3 Session Handoff Protocol

When ending a session that will be continued later:

**Mandatory Handoff Artifacts**:

| Artifact | Location | Content |
|----------|----------|---------|
| **Handoff notes** | `.ai/memory/handoffs/` | What was done, what remains, blockers |
| **TodoWrite state** | Tool state or documented | Current task list with status |
| **Uncommitted changes** | Git stash or WIP commit | Any work not yet committed |
| **Open questions** | Handoff notes | Decisions needed from orchestrator |

**Handoff Note Template**:
```markdown
# Session Handoff: [SLICE-ID]
**Date**: [YYYY-MM-DD HH:MM]
**Session Type**: Implementation / Review / Exploration

## Completed
- [x] Task 1: Description
- [x] Task 2: Description

## In Progress
- [ ] Task 3: Description (state: ...)

## Blocked / Needs Decision
- Question 1: ...

## Next Steps
1. Step to take next
2. ...

## Context for Next Session
- Key insight 1
- Key insight 2
```

### 6.4 Session Boundaries

Sessions must have clear boundaries to prevent drift:

**Session Scope Rules**:
- One slice per implementation session (unless explicitly multi-slice)
- Maximum session duration: defined by slice complexity, not time
- Session ends when: slice complete, blocked, or context limit reached
- Never carry unrelated work into a session

**Boundary Violations to Avoid**:
- Starting work on a different slice without explicit orchestrator approval
- Accumulating context from multiple unrelated features
- Continuing after significant blockers without human input
- Modifying files outside the slice scope

### 6.5 Session Persistence Rules

What to persist vs. discard at session end:

| Artifact Type | Persist? | Location | Retention |
|---------------|----------|----------|-----------|
| **Code changes** | Yes (committed) | Git repository | Permanent |
| **Handoff notes** | Yes | `.ai/memory/handoffs/` | Until slice complete |
| **Session context** | Conditional | `.ai/memory/sessions/` | Short-term (7 days) |
| **Learnings** | Yes (if valuable) | `.ai/memory/learnings/` | Long-term |
| **Temporary files** | No | Delete | N/A |
| **Debug output** | No | Delete | N/A |
| **Sensitive data** | Never | N/A | N/A |

**Persistence Decision Matrix**:
- Will a future session need this? → **Persist**
- Is this specific to this moment only? → **Discard**
- Does this contain secrets or PII? → **Never persist**
- Is this a reusable pattern/learning? → **Persist to learnings**

### 6.6 Multi-Session Workflows

For work spanning multiple sessions:

**Session Tree Pattern**:
```
Human Orchestrator
       │
       ▼
┌──────────────┐
│  Session 1   │  → Handoff notes
│ (Foundation) │
└──────────────┘
       │
       ▼
┌──────────────┐
│  Session 2   │  → Handoff notes
│  (Features)  │
└──────────────┘
       │
       ▼
┌──────────────┐
│  Session 3   │  → Complete
│   (Polish)   │
└──────────────┘
```

**Session Continuity Checklist**:
- [ ] Previous handoff notes reviewed
- [ ] Current git state verified
- [ ] Slice spec re-read (may have updated)
- [ ] Outstanding decisions resolved
- [ ] Context loaded appropriately

### 6.7 Integration with Change Classes

*Note: See Change Classes documentation for detailed risk-based workflow guidance.*

Session management aligns with Change Classes:

| Change Class | Session Approach |
|--------------|------------------|
| **Class A** (trivial) | Single session, minimal handoff |
| **Class B** (bounded) | May span sessions, standard handoff |
| **Class C** (architectural) | Multiple sessions expected, detailed handoffs, human checkpoints |

---

## 7. Change Classes and TodoWrite Tiers

AOSD classifies changes into three classes based on scope, risk, and complexity. This classification determines the appropriate workflow, review level, and TodoWrite tier.

### 7.1 Change Class Definitions

| Class | Name | Scope | Examples |
|-------|------|-------|----------|
| **Class A** | Trivial/Local | Single file, no behavior change | Typo fixes, formatting, comments, import cleanup |
| **Class B** | Bounded/Multi-file | Multiple files, contained impact | Bug fixes, small features, refactors within module |
| **Class C** | Slice-Level/Architectural | Cross-cutting, significant impact | New features, architectural changes, security updates |

### 7.2 Class A: Trivial/Local Changes

**Characteristics**:
- Single file modification (or few lines across 2-3 files)
- No behavioral change or trivial behavioral change
- No impact on other components
- Easily reversible
- No security implications

**Examples**:
- Fix typo in error message
- Update comment or documentation string
- Add/remove import statements
- Code formatting changes
- Rename local variable
- Update version number

**Workflow**:
```
Assess (Class A) → Implement → Fast tests → Commit
```

**Requirements**:
| Aspect | Requirement |
|--------|-------------|
| **TodoWrite** | Skip (Zero tier) |
| **Human Review** | None (automated checks sufficient) |
| **Tests** | Fast tests only |
| **PR Required** | Optional (can commit directly to feature branch) |
| **Session** | Single session, no handoff needed |

### 7.3 Class B: Bounded/Multi-file Changes

**Characteristics**:
- Multiple files within a bounded scope
- Behavioral changes with contained impact
- May affect related components
- Moderate reversibility
- Low-to-medium security consideration

**Examples**:
- Bug fix requiring handler + service changes
- Add validation to existing endpoint
- Refactor module internals
- Add unit tests for existing code
- Update configuration across environments
- Small feature within existing architecture

**Workflow**:
```
Assess (Class B) → TodoWrite (Mini) → Implement → Fast+Medium tests → PR → Review → Merge
```

**Requirements**:
| Aspect | Requirement |
|--------|-------------|
| **TodoWrite** | Mini (3-5 tasks, brief descriptions) |
| **Human Review** | Spot check (focus on risk areas) |
| **Tests** | Fast + Medium tests |
| **PR Required** | Yes |
| **Session** | May span sessions; standard handoff if needed |

### 7.4 Class C: Slice-Level/Architectural Changes

**Characteristics**:
- Cross-cutting changes spanning multiple modules
- Significant behavioral or architectural impact
- Affects many components or users
- Hard to reverse without significant effort
- Security-sensitive or compliance-relevant

**Examples**:
- New feature slice (end-to-end)
- Database schema migration
- Authentication/authorization changes
- API contract changes
- Infrastructure modifications
- Architectural pattern introduction
- Security control updates

**Workflow**:
```
Assess (Class C) → Slice Spec → Architecture Review → TodoWrite (Full) →
Implement → All tests → PR → Multi-reviewer → Human Approval → Staged Deploy → Production
```

**Requirements**:
| Aspect | Requirement |
|--------|-------------|
| **TodoWrite** | Full (detailed tasks, acceptance criteria) |
| **Human Review** | Full review required |
| **Tests** | Fast + Medium + Slow (full suite) |
| **PR Required** | Yes, with detailed description |
| **Session** | Multiple sessions expected; detailed handoffs required |

### 7.5 Change Class Decision Matrix

Use this matrix to classify changes:

| Question | Class A | Class B | Class C |
|----------|---------|---------|---------|
| Files affected | 1-2 | 3-10 | 10+ or cross-module |
| Behavioral change | None/trivial | Contained | Significant |
| Security impact | None | Low | Medium-High |
| Reversibility | Easy | Moderate | Difficult |
| Test requirements | Fast only | Fast+Medium | All tiers |
| User-visible | No | Maybe | Yes |
| Touches invariants | No | Rarely | Possibly |

**Decision Flow**:
```
Is it a single-file, non-behavioral change?
  └─ Yes → Class A
  └─ No  → Does it cross module boundaries or affect architecture?
           └─ Yes → Class C
           └─ No  → Class B
```

### 7.6 TodoWrite Tier Mapping

Change Classes map to TodoWrite tiers from Principle 6:

| Change Class | TodoWrite Tier | Risk Level | Planning Effort |
|--------------|----------------|------------|-----------------|
| **Class A** | **Inline/Skip** (Zero) | Zero risk | None |
| **Class B** | **Mini** (Low-Medium) | Low-Medium | Brief task list |
| **Class C** | **Full** (High) | High | Detailed with acceptance criteria |

### 7.7 Complete Decision Table

| Change Class | Workflow Steps | Review Level | Test Tiers | TodoWrite | Session | Documentation |
|--------------|----------------|--------------|------------|-----------|---------|---------------|
| **A** | Assess → Implement → Test → Commit | Automated only | Fast | Skip | Single | Commit message |
| **B** | Assess → Mini Todo → Implement → Test → PR | Spot check | Fast+Medium | Mini | 1-2 | PR description |
| **C** | Assess → Spec → Review → Full Todo → Implement → Test → PR | Full review | All | Full | Multiple | Slice spec + ADR (if architectural) |

### 7.8 Class Escalation

Changes may escalate to a higher class during implementation:

| Trigger | Action |
|---------|--------|
| Unexpected files need modification | Re-assess class |
| Security implication discovered | Escalate to Class C |
| Invariant touched | Escalate to Class C |
| Tests reveal broader impact | Re-assess and possibly escalate |

**Escalation Protocol**:
1. Stop current implementation
2. Re-classify the change
3. Apply new class requirements
4. Notify Human Orchestrator if escalating to Class C
5. Update TodoWrite if needed

### 7.9 Agent Classification Rules

AI agents must classify changes before proceeding:

```
Before starting any implementation:
1. Analyze the requested change
2. Determine Change Class (A, B, or C)
3. State classification and rationale
4. Apply appropriate workflow
5. If uncertain, default to higher class

Classification announcement example:
"This change is **Class B** (bounded/multi-file):
- Affects 4 files within the auth module
- Behavioral change is contained to login flow
- No security boundary impact
- Will use Mini TodoWrite and Fast+Medium tests"
```

---

## 8. Cost Attribution and Token Tracking

Effective cost management requires tracking AI usage at the slice level. This section defines cost attribution requirements for AOSD workflows.

### 8.1 Why Cost Attribution Matters

- **Budget management**: Predict and control AI costs per feature
- **ROI analysis**: Understand cost-effectiveness of AI assistance
- **Anomaly detection**: Identify runaway sessions early
- **Capacity planning**: Plan for scaling AI usage
- **Accountability**: Attribute costs to specific work items

### 8.2 Per-Slice Cost Tracking

Every slice should track AI costs:

| Metric | Description | Collection Point |
|--------|-------------|------------------|
| **Input tokens** | Tokens sent to AI model | Per request |
| **Output tokens** | Tokens received from AI model | Per request |
| **Total cost** | Calculated cost for slice | Aggregated |
| **Sessions count** | Number of AI sessions | Per slice |
| **Agent role breakdown** | Cost by Builder/Reviewer/Specialist | Per session |

### 8.3 Cost Attribution Schema

```json
{
  "slice_id": "SLICE-042",
  "work_item": "#123",
  "period": {
    "start": "2025-01-15T09:00:00Z",
    "end": "2025-01-15T17:00:00Z"
  },
  "summary": {
    "total_cost": 2.45,
    "total_tokens": {
      "input": 125000,
      "output": 45000
    },
    "session_count": 4
  },
  "by_role": {
    "builder": {
      "cost": 1.85,
      "tokens": { "input": 95000, "output": 35000 },
      "sessions": 3
    },
    "reviewer": {
      "cost": 0.60,
      "tokens": { "input": 30000, "output": 10000 },
      "sessions": 1
    }
  },
  "by_model": {
    "claude-3-5-sonnet-20241022": {
      "cost": 2.45,
      "requests": 47
    }
  }
}
```

### 8.4 Cost Summary in Slice Documentation

Add cost summary to slice completion documentation:

```markdown
## Cost Summary

| Metric | Value |
|--------|-------|
| **Total Cost** | $2.45 |
| **Sessions** | 4 |
| **Duration** | 8 hours |
| **Cost per Hour** | $0.31 |

### Token Usage
| Direction | Tokens | Cost |
|-----------|--------|------|
| Input | 125,000 | $0.38 |
| Output | 45,000 | $2.07 |

### By Agent Role
| Role | Cost | % of Total |
|------|------|------------|
| Builder | $1.85 | 76% |
| Reviewer | $0.60 | 24% |

### Comparison to Baseline
| Metric | This Slice | Baseline | Status |
|--------|------------|----------|--------|
| Cost | $2.45 | $3.00 | Under budget |
| Tokens | 170K | 200K | Efficient |
```

### 8.5 Cost Guardrails

Implement cost guardrails to prevent runaway spending:

| Guardrail | Threshold | Action |
|-----------|-----------|--------|
| **Session warning** | 75% of budget | Alert orchestrator |
| **Session hard stop** | 100% of budget | Pause and require approval |
| **Slice warning** | 80% of estimate | Alert for review |
| **Daily cap** | Organization-defined | Block new sessions |
| **Token spike** | 3x typical rate | Alert for investigation |

### 8.6 Cost Thresholds by Change Class

| Change Class | Expected Cost | Warning Threshold | Hard Stop |
|--------------|---------------|-------------------|-----------|
| **Class A** | $0.10 - $0.50 | $0.75 | $1.00 |
| **Class B** | $0.50 - $3.00 | $4.50 | $6.00 |
| **Class C** | $2.00 - $15.00 | $20.00 | $30.00 |

### 8.7 Cost Logging Requirements

Every AI request should log:

```json
{
  "timestamp": "2025-01-15T14:30:00Z",
  "slice_id": "SLICE-042",
  "session_id": "sess_abc123",
  "request_id": "req_xyz789",
  "agent_role": "builder",
  "model_id": "claude-3-5-sonnet-20241022",
  "tokens": {
    "input": 2500,
    "output": 850
  },
  "cost": {
    "input": 0.0075,
    "output": 0.0383,
    "total": 0.0458
  },
  "latency_ms": 1250
}
```

### 8.8 Cost Optimization Strategies

| Strategy | When to Apply | Expected Savings |
|----------|---------------|------------------|
| **Context pruning** | Large context accumulation | 20-40% |
| **Model tiering** | Simple tasks use smaller models | 50-70% |
| **Caching** | Repeated similar queries | 10-30% |
| **Batch requests** | Multiple independent tasks | 5-15% |
| **Early termination** | Clear completion signal | Variable |

### 8.9 Reporting and Visibility

Provide cost visibility at multiple levels:

| Level | Frequency | Audience |
|-------|-----------|----------|
| **Request** | Real-time | Agent/tooling |
| **Session** | End of session | Orchestrator |
| **Slice** | Slice completion | Orchestrator, stakeholders |
| **Weekly** | Weekly digest | Team, management |
| **Monthly** | Monthly report | Finance, planning |

---

## 9. Constrained Environment Workflows

Some environments have limited AI capabilities:

- Smaller context windows
- Limited multi-file reasoning
- Restricted operations

For these environments:

- Break tasks into atomic units
- Minimize refactoring scope
- Perform architectural work in primary environment
- Sync changes back to source of truth

See your Level 2 profile for specific constrained environment workflows.

---

## 10. Non-Negotiable Workflow Rules

1. **TodoWrite for every feature** - No skipping the planning phase
2. **Follow architecture patterns** - Use established patterns, don't invent new ones
3. **No security bypass** - Never disable security controls
4. **Code lives in development environment** - Not on local machines
5. **No destructive production tests** - Read-only operations only
6. **Follow promotion path** - Dev to Staging to Production
7. **AI follows invariants** - Builder agents must adhere to all documented constraints
8. **Review critical decisions** - Human or Reviewer Agent approval required

---

## 11. Minimal AOSD Mode (Level-0)

For prototypes, experiments, learning, personal projects, and spike work, AOSD provides a **Minimal Mode** that strips the methodology down to its absolute essentials. This allows rapid iteration while maintaining the core safety principles.

*See also: [Adoption Maturity Model](./AOSD_FRAMEWORK.md#-adoption-maturity-model) for the complete Level 0 definition.*

### 11.1 When to Use Level-0

Level-0 is appropriate for:
- **Prototypes**: Quickly validating an idea before committing to full implementation
- **Experiments**: Testing new technologies, libraries, or approaches
- **Learning**: Getting familiar with AOSD or new AI tools
- **Personal projects**: Side projects without external users or production requirements
- **Spike work**: Time-boxed exploration to answer specific questions
- **Hackathons**: Rapid development with throwaway expectations

### 11.2 Level-0 Core Rules

At Level-0, the entire AOSD methodology reduces to three non-negotiable rules:

| Rule | Description |
|------|-------------|
| **Single Invariant** | Don't break existing functionality. If something works, don't make it not work. |
| **Single Trust Rule** | AI agents never touch production systems or handle secrets/credentials. |
| **Human Deploys** | All deployments are human-initiated and human-verified. |

### 11.3 Simplified Workflow

```
Idea → Quick TodoWrite (inline/mental) → Implement → Fast Tests → Commit → Repeat
```

**What's Required**:
- Run fast tests before committing (if tests exist)
- Human reviews changes before any deployment
- Keep AI away from production and secrets

**What's Optional**:
- Formal slice specifications
- Full TodoWrite planning
- Medium and slow tests
- Multi-environment setup
- ADRs and formal documentation
- Code review process
- Cost tracking

### 11.4 Level-0 Agent Behavior

When operating at Level-0, AI agents should:

1. **Ask before breaking**: "This change might break X. Should I proceed?"
2. **Never assume production access**: Always treat production as off-limits
3. **Keep it simple**: Don't over-engineer or add unnecessary abstractions
4. **Favor speed over perfection**: Working code beats perfect code for experiments
5. **Document minimally**: A comment explaining "why" is enough
6. **Test the happy path**: At minimum, verify the main use case works

### 11.5 Upgrading from Level-0

Upgrade to Level-1 (Foundational) when any of these conditions are met:

| Trigger | Why Upgrade |
|---------|-------------|
| Project will be maintained long-term | Future you (or others) will need documentation |
| Others will work on the code | Shared understanding requires explicit invariants |
| Real user data involved | Security and privacy require formal controls |
| Production deployment planned | Deployment safety requires environment isolation |
| Significant rework needed | > 30 minutes fixing something = time for guardrails |
| Wishing for documentation | If you want it documented, document it |

**Transition Checklist** (Level-0 → Level-1):
- [ ] Document 3-5 architectural invariants
- [ ] Write a slice spec for the next feature
- [ ] Set up feature branch workflow
- [ ] Establish Dev and Prod environments (at minimum)
- [ ] Add human review gate before production changes

### 11.6 Compatibility with Core Principles

Level-0 maintains compatibility with AOSD's foundational principles:

| Principle | Level-0 Interpretation |
|-----------|------------------------|
| **Principle 2 (Trustworthy-by-Design)** | The single invariant and trust rule preserve safety |
| **Principle 8 (Human-in-the-Loop)** | Human deploys ensure oversight at critical points |
| **Principle 6 (TodoWrite)** | Inline/mental TodoWrite satisfies planning intent |
| **Principle 7 (Testing)** | Fast tests satisfy minimum testing requirement |

Level-0 is not a bypass of AOSD—it's the minimal viable implementation that still honors the core philosophy.

---

## 12. Workflow Customization

When implementing this workflow for your environment:

### Platform-Specific Choices
- Cloud provider and services
- CI/CD tools
- Deployment mechanisms

### AI Agent Selection
- Primary builder agent
- Reviewer agent
- Specialist agents

### Testing Tools
- Unit test frameworks
- Integration test tools
- E2E test frameworks

Document these choices in your Level 2 Orchestration Profile.

---

## 13. Summary

This workflow operationalizes AI-native engineering using AOSD principles.
It ensures that:

- AI behavior remains predictable
- Architecture stays consistent
- Deployments remain safe
- Environments stay isolated
- Code quality stays high
- Security is never compromised

All AOSD projects should follow this workflow, adapted for their specific platform and toolset.

---

## 14. Zone-Based Workflow Visualization (Optional)

This section describes an **optional, conceptual pattern** for visualizing workflow state across multiple slices and sessions. This pattern is tool-agnostic—it can be implemented with Kanban boards, custom tooling, or even manually.

*Note: This pattern is optional. Many teams operate successfully without formal visualization. Consider adopting it when managing 3+ concurrent slices or coordinating multiple orchestrators.*

### 14.1 Zone Definitions

Workflow visualization uses **zones** representing development stages:

| Zone | Description | Entry Criteria | Exit Criteria |
|------|-------------|----------------|---------------|
| **Analyze** | Understanding requirements, researching approach | Slice assigned | Approach decided |
| **Design** | Creating design, planning TodoWrite | Analysis complete | Design reviewed |
| **Implement** | Active coding, AI agent execution | Design approved | Code complete |
| **Test** | Running tests, fixing failures | Implementation complete | Tests passing |
| **Review** | Code review, human validation | Tests passing | Review approved |
| **Ready** | Ready for merge/deploy | Review approved | Merged/deployed |

### 14.2 Zone Flow Diagram

```
┌──────────┐    ┌──────────┐    ┌──────────┐
│ Analyze  │───▶│  Design  │───▶│Implement │
└──────────┘    └──────────┘    └──────────┘
                                      │
┌──────────┐    ┌──────────┐    ┌─────▼────┐
│  Ready   │◀───│  Review  │◀───│   Test   │
└──────────┘    └──────────┘    └──────────┘
```

### 14.3 Card Representation

Each slice is represented as a "card" with key information:

| Field | Description | Example |
|-------|-------------|---------|
| **Slice ID** | Unique identifier | `SLICE-042` |
| **Title** | Brief description | "Add user authentication" |
| **Assignee** | Current orchestrator/operator | `@alice` |
| **Change Class** | A/B/C classification | `Class B` |
| **TodoWrite Link** | Link to planning document | `[TodoWrite]` |
| **Worktree/Branch** | Isolation mechanism | `feature/auth` |
| **Status** | Current state within zone | `In Progress` |
| **Blockers** | Any impediments | `Waiting on API design` |

**Card Example**:
```
┌────────────────────────────────┐
│ SLICE-042                [B]   │
│ Add user authentication        │
│ ──────────────────────────────│
│ @alice    branch: feature/auth │
│ Status: In Progress            │
│ [TodoWrite] [PR #123]          │
│ ⚠ Blocked: Waiting on API spec │
└────────────────────────────────┘
```

### 14.4 Zone Movement Rules

**Critical Safety Rules**:

1. **Orchestrator-Initiated**: Zone movement is always initiated by the Human Orchestrator, never by AI agents.

2. **No Shared Environments**: Slices in the same zone must NOT share development environments. Each slice uses its own:
   - Git worktree or branch
   - Development environment instance
   - Session context

3. **Sequential Gates**: Slices cannot skip zones (e.g., cannot go from Analyze directly to Review).

4. **Backward Movement Allowed**: Slices can move backward (e.g., Review → Implement for fixes).

**Zone Transition Checklist**:

| Transition | Checklist |
|------------|-----------|
| Analyze → Design | [ ] Requirements understood [ ] Approach documented |
| Design → Implement | [ ] Design reviewed [ ] TodoWrite created |
| Implement → Test | [ ] Code complete [ ] Fast tests passing |
| Test → Review | [ ] All test tiers passing [ ] No blockers |
| Review → Ready | [ ] Code review approved [ ] UAT passed |
| Ready → (Done) | [ ] Merged [ ] Deployed (if applicable) |

### 14.5 Multi-Slice Coordination

When multiple slices are active simultaneously:

| Consideration | Guideline |
|---------------|-----------|
| **WIP Limits** | 2-4 slices per orchestrator (solo), 6-8 with tooling |
| **Conflict Detection** | Check for overlapping file modifications before zone changes |
| **Merge Order** | PRIMARY branches merge before SECONDARY (see PARALLEL_DEVELOPMENT.md) |
| **Stale Detection** | Flag slices idle in a zone > 3 days |

### 14.6 Visualization Options

This pattern can be implemented with various tools:

| Tool Type | Examples | Notes |
|-----------|----------|-------|
| **Kanban boards** | Jira, Trello, GitHub Projects | Native zone/column support |
| **Markdown tables** | Manual in README/wiki | Simple, no tooling required |
| **Custom dashboards** | Internal tools | Most flexible, most effort |
| **CLI tools** | Custom scripts | Developer-friendly |

**Example: Simple Markdown Board**:
```markdown
## Workflow Board

### Analyze
- [ ] SLICE-045: API versioning strategy

### Design
- [ ] SLICE-044: Payment integration

### Implement
- [x] SLICE-042: User authentication (@alice)
- [ ] SLICE-043: Dashboard UI (@bob)

### Test
(empty)

### Review
(empty)

### Ready
- [x] SLICE-041: Login page (pending merge)
```

### 14.7 Cross-References

This pattern connects to:
- [Change Classes](./DEVELOPMENT_WORKFLOW.md#7-change-classes-and-todowrite-tiers) - Risk classification (#82)
- [AI Session Management](./DEVELOPMENT_WORKFLOW.md#6-ai-session-management) - Session-to-slice association (#85)
- [Multi-Agent Patterns](./MULTI_AGENT_PATTERNS.md) - Parallel development coordination (#90)
- [Agent Communication](./AGENT_COMMUNICATION.md) - Hub-and-spoke for zone updates (#91)

### 14.8 When NOT to Use This Pattern

Zone-based visualization may be **overkill** for:
- Solo developers with 1-2 active slices
- Very small projects (< 5 total slices)
- Level-0 (Minimal Mode) projects
- Short-lived experiments or spikes

Consider adopting when:
- Managing 3+ concurrent slices
- Multiple orchestrators need coordination
- Stakeholders need visibility into progress
- Past issues with slice state confusion

---

## 15. Related Documents

- [AOSD Framework](./AOSD_FRAMEWORK.md) - Core methodology
- [Environment Strategy](./ENVIRONMENT_STRATEGY.md) - Environment isolation principles
- [Agent Communication & Protocols](./AGENT_COMMUNICATION.md) - Communication architecture and protocols
- [Anti-Patterns](./ANTI_PATTERNS.md) - Patterns to avoid in AOSD development
- [Multi-Agent Patterns](./MULTI_AGENT_PATTERNS.md) - Safe patterns for multi-agent orchestration
- [Parallel Development](./PARALLEL_DEVELOPMENT.md) - Running multiple AI agent sessions simultaneously
- [AI Prompting Patterns](./AI_PROMPTING_PATTERNS.md) - Prompting best practices
- [Documentation Governance](./DOCUMENTATION_GOVERNANCE.md) - Multi-level documentation management
- [AWS + Claude Workflow Example](./examples/reference-implementations/workflows/aws-claude-workflow.md) - AWS-specific implementation

---

**End of DEVELOPMENT_WORKFLOW.md**
