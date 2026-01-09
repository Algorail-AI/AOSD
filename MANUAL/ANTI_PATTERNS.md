# AOSD Anti-Patterns

**Patterns That Violate AOSD Principles and Must Be Avoided**

---

## 1. Purpose

This document catalogs **anti-patterns**—practices that violate AOSD principles and should never occur in AOSD-governed systems. Each anti-pattern includes:

- **What's prohibited**: The specific behavior that violates AOSD
- **Why it's dangerous**: The risks and consequences
- **Principles violated**: Which AOSD principles this breaks
- **What IS allowed**: The correct alternative approach

Use this document to:
- Train AI agents on what NOT to do
- Review systems for anti-pattern presence
- Establish guardrails in prompts and workflows

---

## 2. Anti-Pattern Categories

### 2.1 Shared Development Environments

**What's Prohibited**:
- Multiple developers or agents working in the same development environment simultaneously
- Sharing environment credentials across sessions or users
- Running parallel agent sessions in the same filesystem without isolation
- Deploying from multiple branches to the same environment concurrently

**Why It's Dangerous**:
- File conflicts corrupt work in progress
- Race conditions lead to undefined system state
- Deployment collisions create unpredictable environments
- Debugging becomes impossible when changes interleave
- Security boundaries break down with shared credentials

**Principles Violated**:
- **Principle 3 (Environment Isolation)**: Environments must have clear boundaries
- **Principle 8 (Human-in-the-Loop)**: Loss of control over environment state
- **Principle 10 (Version Control)**: Branch integrity compromised

**What IS Allowed**:
- **Isolated sessions**: Use git worktrees or separate clones for parallel work
- **Session ownership**: Each agent session works in its own directory
- **Deployment locks**: Only one session (PRIMARY) deploys at a time
- **Credential isolation**: Per-developer or per-session credentials
- **Coordination files**: Explicit coordination protocols (see PARALLEL_DEVELOPMENT.md)

---

### 2.2 Unbounded Agent Spawning

**What's Prohibited**:
- Spawning unlimited sub-agents without explicit limits
- Allowing agents to spawn agents recursively without termination conditions
- Running more agents than the orchestrator can monitor
- Agent spawning without Human Orchestrator approval

**Why It's Dangerous**:
- Resource exhaustion (CPU, memory, API costs)
- Loss of orchestrator visibility and control
- Cascading failures across agent tree
- Unpredictable system behavior
- Cost explosion from uncontrolled API calls

**Principles Violated**:
- **Principle 1 (Multi-Agent Orchestration)**: Human Orchestrator must direct all agents
- **Principle 8 (Human-in-the-Loop)**: Loss of strategic control
- **Principle 2 (Trustworthy-by-Design)**: Unpredictable system behavior

**What IS Allowed**:
- **Hard limits**: Maximum 5 agents per slice, maximum 3 reviewers per PR
- **Explicit spawning**: Human Orchestrator initiates all multi-agent workflows
- **Bounded recursion**: Sub-agents cannot spawn their own sub-agents without approval
- **Monitoring capacity**: Never run more agents than orchestrator can actively track (2-4 slices solo, 6-8 with tooling support)
- **Cost ceilings**: Set token/API budget limits before spawning agents

---

### 2.3 Peer-to-Peer Agent Communication

**What's Prohibited**:
- Agents directly communicating with each other without human mediation
- Agent A instructing Agent B without orchestrator awareness
- Autonomous coordination between agents
- Agents modifying each other's context or state
- Shared mutable state between concurrent agents

**Why It's Dangerous**:
- Human Orchestrator loses visibility into agent coordination
- Emergent behavior not sanctioned by orchestrator
- Accountability becomes impossible to trace
- Security boundaries bypassed
- Conflicting instructions without arbitration

**Principles Violated**:
- **Principle 1 (Multi-Agent Orchestration)**: All agents operate under Human Orchestrator direction
- **Principle 8 (Human-in-the-Loop)**: Human must mediate agent interactions
- **Principle 2 (Trustworthy-by-Design)**: Auditability requires traceable decisions

**What IS Allowed**:
- **Hub-and-spoke**: Human Orchestrator mediates all inter-agent communication
- **Explicit handoffs**: Agent A produces artifact → Human reviews → Human passes to Agent B
- **Read-only shared context**: Agents can read shared documentation, not modify it autonomously
- **Structured coordination files**: PARALLEL_DEV_COORDINATION.md with human oversight
- **Async artifacts**: Agents communicate via committed code, PRs, and documentation—not direct messages

---

### 2.4 Trust Zone Violations

**What's Prohibited**:
- Agents operating outside their assigned trust level
- Skipping approval gates for operations requiring human review
- Autonomous execution of Trust Level 0-2 operations
- Elevating agent autonomy without explicit orchestrator authorization
- Production access from development agents

**Why It's Dangerous**:
- Security controls bypassed
- Irreversible changes made without oversight
- Compliance violations in regulated environments
- Data loss or corruption from unauthorized operations
- Audit trail gaps

**Principles Violated**:
- **Principle 8 (Human-in-the-Loop)**: Trust zones define required oversight
- **Principle 3 (Environment Isolation)**: Cross-environment access violates boundaries
- **Principle 2 (Trustworthy-by-Design)**: Security controls are architectural, not optional

**What IS Allowed**:
- **Trust level adherence**: Agents operate strictly within assigned trust levels
- **Explicit escalation**: When higher autonomy is needed, request orchestrator approval
- **Trust level documentation**: Slice specs declare trust levels for operations
- **Environment-appropriate access**: Development agents only access development resources
- **Approval gates enforced**: Trust Levels 0-3 operations require human approval before execution

**HITL Trust Level Reference**:

| Level | Name | What Agents CAN Do | What Requires Human |
|-------|------|-------------------|---------------------|
| 0 | Full Human | Nothing | Everything |
| 1 | AI Assists | Suggest | Decide and execute |
| 2 | AI Drafts | Create artifacts | Approve and execute |
| 3 | AI Executes | Execute with approval | Approve before execution |
| 4 | AI Autonomous | Execute freely | Post-hoc review |
| 5 | AI Silent | Execute freely | Nothing |

---

### 2.5 Long-Running Sessions Without Slice Boundaries

**What's Prohibited**:
- Continuous agent sessions that span multiple unrelated features
- Sessions without clear start/end boundaries
- Accumulating context across unrelated work items
- "Marathon sessions" that run indefinitely
- Abandoning slice specs mid-implementation to work on something else

**Why It's Dangerous**:
- Context pollution leads to cross-contamination of features
- Agent drift as context becomes stale or confused
- Accountability gaps—which changes belong to which work item?
- Cost accumulation from oversized contexts
- Increased error rates as session length grows
- Difficulty recovering from failures

**Principles Violated**:
- **Principle 5 (Slice-Based Development)**: Work must be bounded by slice specifications
- **Principle 6 (TodoWrite Workflow)**: Tasks must be planned and tracked explicitly
- **Principle 11 (Work Item Lifecycle)**: Work must be traceable to work items

**What IS Allowed**:
- **Slice-bounded sessions**: One session = one slice (or explicit set of related slices)
- **Clear session lifecycle**: Start with spec, end with completion or explicit handoff
- **Context reset**: New slice = fresh context with relevant documentation loaded
- **Time boxing**: Set session time limits, especially for complex work
- **Handoff protocols**: Write handoff notes before ending incomplete sessions
- **Session documentation**: Record what was accomplished, what remains

---

### 2.6 Non-Deterministic and Unlogged Actions

**What's Prohibited**:
- Operations that cannot be reproduced or audited
- Generating code or making changes without logging
- Using randomness in ways that affect reproducibility
- Skipping commit messages or ADRs for significant changes
- Silent modifications to shared resources
- Operations that cannot be rolled back

**Why It's Dangerous**:
- Debugging becomes impossible without reproducibility
- Compliance failures from missing audit trails
- Inability to understand what changed or why
- Rollback impossible without change documentation
- Security incidents undetectable without logs
- Knowledge loss when original context is gone

**Principles Violated**:
- **Principle 2 (Trustworthy-by-Design)**: All operations must be traceable and reversible
- **Principle 10 (Version Control)**: Changes must be committed with meaningful messages
- **Principle 12 (Self-Documenting Systems)**: Decisions must be documented

**What IS Allowed**:
- **Deterministic operations**: Given the same inputs, produce the same outputs
- **Comprehensive logging**: All significant operations logged with context
- **Meaningful commits**: Every change committed with descriptive message
- **Decision documentation**: ADRs for architectural choices, comments for non-obvious code
- **Seeded randomness**: When randomness is needed, use seeds that can be reproduced
- **Audit trails**: Maintain traceability from work item → changes → deployment

---

### 2.7 Unauthorized Architecture Edits

**What's Prohibited**:
- Modifying architectural invariants without Human Orchestrator approval
- Introducing new patterns not in the pattern library
- Changing infrastructure without documented authorization
- Bypassing or disabling security controls
- Modifying shared foundations (schemas, APIs, auth) without review
- "Quick fixes" that violate established patterns

**Why It's Dangerous**:
- Architecture drift makes systems unmaintainable
- Security holes from unauthorized changes
- Integration failures from incompatible patterns
- Technical debt accumulation
- Loss of architectural consistency
- Future agents confused by conflicting patterns

**Principles Violated**:
- **Principle 4 (Architectural Invariants)**: Invariants are non-negotiable
- **Principle 2 (Trustworthy-by-Design)**: Security controls are architectural
- **Principle 1 (Multi-Agent Orchestration)**: Human Orchestrator defines architecture

**What IS Allowed**:
- **Invariant adherence**: Follow all documented architectural invariants
- **Pattern library usage**: Use only patterns from the approved pattern library
- **Proposal workflow**: Propose new patterns via ADR for Human Orchestrator review
- **Explicit authorization**: Architectural changes require documented approval
- **Security preservation**: Never bypass, disable, or weaken security controls
- **Infrastructure as Code**: All infrastructure changes through IaC with review

**Examples of Unauthorized Changes**:

| Category | Unauthorized | Authorized Alternative |
|----------|--------------|----------------------|
| New API pattern | Inventing REST conventions | Use documented API patterns |
| Database access | Direct DB calls from handlers | Use service factory pattern |
| Authentication | Custom auth implementation | Use established auth layer |
| Error handling | Ad-hoc try/catch everywhere | Follow error handling pattern |
| Dependencies | Adding new frameworks | Propose via ADR, get approval |

---

### 2.8 Refactoring Without Measurement

**What's Prohibited**:
Proposing or executing architectural changes justified by performance, deploy speed, or scale without first measuring the dominant bottleneck. Structural smells (large files, monolithic stacks, complex configs) are hypotheses about problems, not confirmed causes.

- Assuming large file size or structural complexity causes performance issues
- Refactoring for "cleanliness" when the justification is performance
- Acting on agent recommendations without validating the underlying premise
- Introducing migration risk to solve a problem that hasn't been measured

**What IS Allowed**:
- Measure the dominant bottleneck before proposing changes
- Treat structural concerns as hypotheses requiring validation
- Refactor for maintainability when that's the actual goal (not disguised performance claims)

**See**: [Lambda Deploy Optimization Case Study](./examples/reference-implementations/case-studies/lambda-deploy-optimization-case-study.md) for an example.

---

### 2.9 Implicit Authority

**What's Prohibited**:
- Assuming branch authority without explicit rules
- Deploying from feature branches because "it works"
- Allowing multiple sources of "deployable truth"
- Relying on convention rather than enforcement for deployment authority
- Treating test success as deployment authorization

**Why It's Dangerous**:
When multiple agents or humans work concurrently, any ambiguity about what constitutes authoritative state will eventually cause work loss. A fix that exists only in a feature branch can be:
1. Deployed and appear to work
2. Never merged to main
3. Silently overwritten by a subsequent deployment from main

This isn't a "multi-agent problem"—it's an authority problem that multi-agent work surfaces faster. In single-human workflows, the same failure happens slower (hotfix locally, deploy from wrong branch, assume it'll get merged later, another deploy overwrites it). Multi-agent work compresses time and removes the illusion of safety.

**Principles Violated**:
- **Principle 10 (Version Control)**: Main must be the single source of truth
- **Principle 4 (Architectural Invariants)**: Authority rules are invariants
- **Principle 8 (Human-in-the-Loop)**: Human must control what constitutes deployable state

**What IS Allowed**:
- **Explicit authority**: "Only main represents deployable truth" as an enforced invariant
- **Mechanical verification**: Pre-deploy checks validate `git merge-base --is-ancestor HEAD origin/main`
- **Merge-before-deploy**: All work must be merged to main before deployment
- **Deployment provenance**: Every deployment records commit, branch, PR, agent, timestamp
- **Branch cleanup**: Delete feature branches after merge to remove authority ambiguity
- **Role-based deploy authority**: Only designated agents/humans may deploy, and only from main

**Authority Enforcement Checklist**:

| Check | Enforcement |
|-------|-------------|
| Commit on main? | `git merge-base --is-ancestor HEAD origin/main` |
| Provenance captured? | Deployment logs include commit, branch, agent, timestamp |
| Feature branches cleaned? | Auto-delete on merge enabled |
| Deploy authority clear? | Only Human Orchestrator or designated deploy agent can deploy |

**Recovery When Authority Drift Occurs**:
1. Stop: Halt all deployments
2. Identify: Which branch is actually deployed? Which should be?
3. Merge: Get the correct state into main
4. Deploy: Re-deploy from main
5. Enforce: Add the pre-deploy ancestry check to prevent recurrence

**See**: [Multi-Agent Patterns - Authority and State Management](./MULTI_AGENT_PATTERNS.md#7-authority-and-state-management) and [Invariants Cookbook - Deployment Authority](./INVARIANTS_COOKBOOK.md#11-deployment-authority-invariants) for the correct patterns.

---

## 3. Detection and Prevention

### 3.1 Anti-Pattern Detection Checklist

Use this checklist during code review and system audits:

**Environment & Sessions**:
- [ ] Each agent session has its own isolated workspace
- [ ] No shared credentials across sessions
- [ ] Coordination files exist for parallel work
- [ ] Session boundaries align with slice boundaries

**Agent Management**:
- [ ] Agent count within limits (≤5 per slice, ≤3 reviewers)
- [ ] All agent spawning initiated by Human Orchestrator
- [ ] No peer-to-peer agent communication
- [ ] Clear agent roles documented

**Trust & Authorization**:
- [ ] Operations match assigned trust levels
- [ ] Approval gates enforced for Trust Levels 0-3
- [ ] No production access from development agents
- [ ] Trust levels documented in slice specs

**Traceability**:
- [ ] All changes committed with meaningful messages
- [ ] ADRs exist for architectural decisions
- [ ] Operations are logged and auditable
- [ ] Changes traceable to work items

**Architecture**:
- [ ] Only approved patterns in use
- [ ] Invariants followed without exception
- [ ] Security controls intact
- [ ] No unauthorized infrastructure changes

### 3.2 Prompt Guardrails

Include these guardrails in AI agent prompts:

```
AI Contract - Anti-Pattern Prevention:
  * Do NOT work in shared environments without isolation
  * Do NOT spawn agents without explicit limits and approval
  * Do NOT communicate directly with other agents
  * Do NOT exceed assigned trust level
  * Do NOT run marathon sessions across multiple slices
  * Do NOT make changes without logging and commits
  * Do NOT modify architecture without Human Orchestrator approval
  * ALWAYS follow documented invariants and patterns
  * ALWAYS operate within slice boundaries
  * ALWAYS request human approval for Trust Level 0-3 operations
```

### 3.3 Automated Enforcement

Where possible, implement automated checks:

| Anti-Pattern | Automated Check |
|--------------|-----------------|
| Shared environments | CI/CD blocks concurrent deployments from different branches |
| Unbounded spawning | Agent frameworks enforce maximum child count |
| Trust violations | Production deployments require approval workflow |
| Unlogged actions | Pre-commit hooks require commit messages |
| Unauthorized architecture | Static analysis checks for pattern compliance |

---

## 4. Recovery from Anti-Pattern Violations

When anti-patterns are detected:

### Immediate Actions
1. **Stop**: Halt the violating operation or session
2. **Assess**: Determine the scope and impact of the violation
3. **Document**: Record what happened and why
4. **Rollback**: Revert unauthorized changes if possible

### Remediation
1. **Root cause**: Understand why the anti-pattern occurred
2. **Fix**: Address the specific violation
3. **Prevent**: Add guardrails to prevent recurrence
4. **Train**: Update prompts and documentation

### Escalation Triggers
- Security control bypassed → Immediate security review
- Production data affected → Incident response
- Multiple violations → Process review with team
- Systemic pattern → Framework/tooling improvement

---

## 5. Related Documents

- [AOSD Framework](./AOSD_FRAMEWORK.md) - Core principles referenced throughout
- [Agent Communication & Protocols](./AGENT_COMMUNICATION.md) - Allowed and prohibited communication patterns
- [Development Workflow](./DEVELOPMENT_WORKFLOW.md) - Correct workflow patterns
- [AI Prompting Patterns](./AI_PROMPTING_PATTERNS.md) - Correct prompting approaches
- [Troubleshooting](./TROUBLESHOOTING.md) - Recovery guidance for drift and errors
- [Multi-Agent Patterns](./MULTI_AGENT_PATTERNS.md) - Safe multi-agent orchestration patterns

---

**End of ANTI_PATTERNS.md**
