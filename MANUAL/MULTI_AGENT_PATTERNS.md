# Multi-Agent Orchestration Patterns

**Safe Patterns for Coordinating Multiple AI Agents in AOSD**

---

## 1. Purpose

This document defines **safe, proven patterns** for orchestrating multiple AI agents within AOSD projects. Each pattern includes:

- **When to use it**: Appropriate scenarios
- **How it works**: Structure and workflow
- **Safety guardrails**: Limits and controls
- **Failure modes**: What can go wrong and how to recover

**Critical Requirement**: All multi-agent patterns require **Human Orchestrator initiation**. Agents must never autonomously spawn other agents or create multi-agent workflows.

---

## 2. Safety Guardrails (All Patterns)

These guardrails apply to **every** multi-agent pattern:

### 2.1 Hard Limits

| Resource | Maximum | Rationale |
|----------|---------|-----------|
| **Agents per slice** | 5 | Orchestrator visibility limit |
| **Reviewers per PR** | 3 | Diminishing returns beyond 3 |
| **Parallel slices (solo)** | 2-4 | Human cognitive limit |
| **Parallel slices (with tooling)** | 6-8 | Requires coordination tooling |
| **Sub-agent depth** | 2 levels | Prevent recursive spawning |
| **Session duration** | Slice-bounded | Prevent context pollution |

### 2.2 Trust Zone Enforcement

- All agents operate within assigned trust levels (see Principle 8)
- Child agents inherit parent's trust level or lower—never higher
- Trust Level 0-2 operations always require Human Orchestrator approval
- Production operations require Trust Level 0-1 (human-controlled)

### 2.3 Communication Rules

- **No peer-to-peer**: Agents never communicate directly with each other
- **Hub-and-spoke**: Human Orchestrator mediates all inter-agent communication
- **Artifact-based handoffs**: Agents communicate via code, PRs, and documentation
- **Read-only shared context**: Agents may read shared docs but not modify them autonomously

### 2.4 Failure Mode Controls

- **Automatic termination**: Sessions that exceed time/token limits terminate gracefully
- **Circuit breakers**: Repeated failures trigger escalation to Human Orchestrator
- **Rollback capability**: All changes must be reversible
- **Audit trail**: All agent actions logged for post-hoc review

---

## 3. Pattern Catalog

### 3.1 Architect + N Builders

**When to Use**:
- Large features requiring multiple parallel implementations
- Features with clear subsystem boundaries
- When one agent can design while others implement

**Structure**:
```
                    Human Orchestrator
                           │
                           ▼
                    ┌─────────────┐
                    │  Architect  │  (designs, coordinates)
                    │    Agent    │
                    └─────────────┘
                           │
            ┌──────────────┼──────────────┐
            ▼              ▼              ▼
      ┌──────────┐  ┌──────────┐  ┌──────────┐
      │ Builder  │  │ Builder  │  │ Builder  │
      │ Agent 1  │  │ Agent 2  │  │ Agent 3  │
      └──────────┘  └──────────┘  └──────────┘
```

**Workflow**:
1. Human Orchestrator defines slice and spawns Architect Agent
2. Architect Agent produces design document and subsystem specs
3. Human Orchestrator reviews design, approves or requests changes
4. Human Orchestrator spawns Builder Agents with subsystem specs
5. Builder Agents implement independently in isolated worktrees
6. Human Orchestrator coordinates merge order

**Safety Guardrails**:
- Maximum 4 Builder Agents per Architect
- Architect cannot spawn Builders—only Human Orchestrator can
- Each Builder works in isolated worktree
- Human Orchestrator reviews Architect output before spawning Builders

**Failure Modes**:
| Failure | Detection | Recovery |
|---------|-----------|----------|
| Design conflicts between subsystems | Integration failures | Reconvene at design level |
| Builder exceeds scope | PR review catches | Reject, re-scope |
| Architect drifts from requirements | Design review | Iterate design before implementation |

---

### 3.2 Builder-Reviewer Chain

**When to Use**:
- Standard feature development
- When quality gates are important
- For changes requiring independent verification

**Structure**:
```
Human Orchestrator
       │
       ▼
┌──────────┐     ┌──────────┐     ┌──────────┐
│  Builder │ ──► │ Reviewer │ ──► │  Human   │
│  Agent   │     │  Agent   │     │ Approval │
└──────────┘     └──────────┘     └──────────┘
       │               │                │
       ▼               ▼                ▼
    Code PR      Review Comments    Merge Decision
```

**Workflow**:
1. Human Orchestrator spawns Builder Agent with slice spec
2. Builder Agent implements, creates PR
3. Human Orchestrator spawns Reviewer Agent to review PR
4. Reviewer Agent provides feedback, Builder iterates
5. Human Orchestrator makes final merge decision

**Safety Guardrails**:
- One Builder, one Reviewer per slice (unless complexity warrants more)
- Reviewer cannot modify code—only comment
- Human Orchestrator approves before any merge
- Maximum 3 review cycles before human escalation

**Failure Modes**:
| Failure | Detection | Recovery |
|---------|-----------|----------|
| Endless review loop | Cycle count exceeds 3 | Human Orchestrator intervenes |
| Reviewer approves bad code | Post-merge issues | Add to test suite, update invariants |
| Builder ignores feedback | Repeated issues | Direct human review |

---

### 3.3 Bounded Reviewer Swarm

**When to Use**:
- High-risk changes (security, data, infrastructure)
- Architectural changes affecting multiple systems
- When diverse perspectives are valuable

**Structure**:
```
                Human Orchestrator
                       │
                       ▼
                ┌──────────┐
                │ Builder  │
                │  Agent   │
                └──────────┘
                       │
                       ▼
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
  ┌──────────┐  ┌──────────┐  ┌──────────┐
  │ Reviewer │  │ Reviewer │  │ Reviewer │
  │ Agent 1  │  │ Agent 2  │  │ Agent 3  │
  │(Security)│  │ (Arch)   │  │ (Domain) │
  └──────────┘  └──────────┘  └──────────┘
        │              │              │
        └──────────────┼──────────────┘
                       ▼
                Human Synthesis
```

**Workflow**:
1. Builder Agent creates implementation
2. Human Orchestrator spawns 2-3 specialized Reviewers in parallel
3. Each Reviewer focuses on their specialty (security, architecture, domain)
4. Human Orchestrator synthesizes feedback, prioritizes issues
5. Builder addresses prioritized feedback
6. Human Orchestrator makes merge decision

**Safety Guardrails**:
- **Maximum 3 Reviewers** (hard limit—diminishing returns beyond)
- Reviewers cannot communicate with each other
- Human synthesizes conflicting feedback
- Time-boxed reviews (prevent endless analysis)

**Failure Modes**:
| Failure | Detection | Recovery |
|---------|-----------|----------|
| Conflicting reviewer opinions | Human synthesis step | Orchestrator decides priority |
| Reviewer scope creep | Reviews include unrelated concerns | Filter to relevant scope |
| All reviewers miss issue | Post-deployment problems | Add to review checklist, update patterns |

---

### 3.4 Sub-Agent Tasks

**When to Use**:
- Complex tasks that benefit from decomposition
- When a primary agent needs specialized help
- Research or exploration tasks

**Structure**:
```
Human Orchestrator
       │
       ▼
┌──────────────┐
│   Primary    │
│    Agent     │
└──────────────┘
       │
       ├── Request: "Research X"
       │         │
       │         ▼
       │   ┌──────────┐
       │   │ Sub-Agent│ (Research)
       │   └──────────┘
       │         │
       │         ▼
       ├── Result returned
       │
       ▼
  Continue work
```

**Workflow**:
1. Human Orchestrator spawns Primary Agent
2. Primary Agent identifies need for sub-task (e.g., research, exploration)
3. Primary Agent **requests** Human Orchestrator spawn sub-agent
4. Human Orchestrator approves and spawns Sub-Agent
5. Sub-Agent completes task, returns result to Human Orchestrator
6. Human Orchestrator passes result to Primary Agent
7. Primary Agent continues with sub-task result

**Safety Guardrails**:
- Primary Agent cannot spawn sub-agents directly—must request
- Maximum 2 levels of sub-agents
- Sub-agents inherit parent trust level or lower
- Sub-agent results pass through Human Orchestrator

**Failure Modes**:
| Failure | Detection | Recovery |
|---------|-----------|----------|
| Sub-agent scope exceeds request | Result review | Reject, re-scope |
| Recursive sub-agent requests | Depth counter | Hard stop at depth 2 |
| Sub-agent contradicts primary | Result inconsistency | Human arbitrates |

---

### 3.5 Session Trees

**When to Use**:
- Long-running projects with natural breakpoints
- When preserving context across time is important
- Projects requiring handoffs between sessions

**Structure**:
```
Human Orchestrator
       │
       ▼
┌──────────────┐
│   Session 1  │ (Day 1: Foundation)
│   Builder    │
└──────────────┘
       │
       ▼ Handoff Document
       │
┌──────────────┐
│   Session 2  │ (Day 2: Features)
│   Builder    │
└──────────────┘
       │
       ▼ Handoff Document
       │
┌──────────────┐
│   Session 3  │ (Day 3: Polish)
│   Builder    │
└──────────────┘
```

**Workflow**:
1. Human Orchestrator plans session tree (what each session will accomplish)
2. Session 1 executes, produces handoff document
3. Human Orchestrator reviews handoff, decides if Session 2 should proceed
4. Session 2 loads handoff context, continues work
5. Repeat until project complete

**Safety Guardrails**:
- Each session is slice-bounded
- Handoff documents required between sessions
- Human Orchestrator reviews handoffs before next session
- Sessions do not share mutable state—only artifacts

**Failure Modes**:
| Failure | Detection | Recovery |
|---------|-----------|----------|
| Context loss between sessions | Session 2 confused | Improve handoff doc, restart session |
| Session scope creep | Handoff includes unplanned work | Reject, refocus on planned scope |
| Handoff document incomplete | Next session struggles | Human fills gaps before continuing |

---

### 3.6 Multi-Agent Parallel Builders

**When to Use**:
- Multiple independent features needed simultaneously
- Clear separation between features (no shared code)
- When velocity is critical and features are well-specified

**Structure**:
```
                Human Orchestrator
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
  ┌──────────┐  ┌──────────┐  ┌──────────┐
  │ Builder  │  │ Builder  │  │ Builder  │
  │ Agent 1  │  │ Agent 2  │  │ Agent 3  │
  │(Feature A)│ │(Feature B)│ │(Feature C)│
  └──────────┘  └──────────┘  └──────────┘
  (worktree A)  (worktree B)  (worktree C)
        │              │              │
        ▼              ▼              ▼
     PR for A      PR for B      PR for C
        │              │              │
        └──────────────┼──────────────┘
                       ▼
              Coordinated Merge
```

**Workflow**:
1. Human Orchestrator identifies independent features
2. Human Orchestrator creates worktrees for isolation
3. Human Orchestrator spawns Builders with feature specs
4. Builders work in parallel, isolated environments
5. Human Orchestrator reviews PRs, determines merge order
6. Merge PRIMARY first, then SECONDARY after rebase

**Safety Guardrails**:
- Maximum 3 parallel Builders (solo orchestrator)
- Each Builder in isolated worktree
- Clear PRIMARY/SECONDARY designation
- No shared code modifications
- Coordination file protocol required
- Human Orchestrator monitors all sessions

**Failure Modes**:
| Failure | Detection | Recovery |
|---------|-----------|----------|
| Feature overlap discovered | Merge conflicts | Stop one Builder, redesign boundaries |
| Resource contention | System slowdown | Pause SECONDARY, let PRIMARY complete |
| Merge conflicts | Git rebase fails | Human resolves, complete merge manually |

---

### 3.7 Coordination Session Pattern

**When to Use**:
- Running 2+ parallel worker sessions that may have interdependencies
- Work items have potential for boundary overlap
- You need structured coordination without constant context-switching
- Scaling beyond what ad-hoc human coordination can manage

**Structure**:
```
                    Human Orchestrator
                           │
                           ▼
                  ┌─────────────────┐
                  │  Coordination   │  (read-only, plans, monitors)
                  │    Session      │
                  └─────────────────┘
                           │
            ┌──────────────┼──────────────┐
            ▼              ▼              ▼
      ┌──────────┐  ┌──────────┐  ┌──────────┐
      │  Worker  │  │  Worker  │  │  Worker  │
      │ Session 1│  │ Session 2│  │ Session 3│
      └──────────┘  └──────────┘  └──────────┘
      (feature-a)   (feature-b)   (feature-c)
```

**Key Distinction**: The Coordination Session is **not** a replacement for the Human Orchestrator. The human remains final authority. The Coordination Session is an AI assistant that helps with:
- Tracking work item status and ownership
- Detecting potential conflicts before they occur
- Drafting renegotiation proposals when boundaries shift
- Formatting alerts for human decision-making

**Workflow**:
1. Human Orchestrator starts Coordination Session first
2. Coordination Session validates work items (open, scoped, no blockers)
3. Human Orchestrator spawns Worker Sessions with ownership declarations
4. Workers implement; Coordination Session monitors for conflicts
5. When conflicts arise, Coordination Session drafts alert for human
6. Human Orchestrator decides resolution, communicates to affected workers
7. Coordination Session tracks merge readiness and sequencing

**Communication Reality**:
- **Today**: Agents cannot message each other directly. Human Orchestrator copies relevant information between sessions using structured alert formats (see [Section 9.3](#93-human-alert-format)).
- **Future**: Agent-to-agent messaging may automate the copy/paste step, but human approval gates remain.

**Safety Guardrails**:
- Coordination Session is read-only by default (no direct code changes)
- Workers cannot communicate directly—all coordination flows through human
- Ownership boundaries must be declared before work begins
- Overlap requires explicit human approval and updated boundaries
- Merge/deploy authority follows [Section 7](#7-authority-and-state-management) rules

**Failure Modes**:
| Failure | Detection | Recovery |
|---------|-----------|----------|
| Coordination Session loses track of state | Conflicting status reports | Human reconciles, restart coordination |
| Worker exceeds declared boundary | PR touches undeclared files | Trigger renegotiation protocol |
| Coordination Session gives bad advice | Human review catches | Override; coordination is advisory only |
| Human becomes bottleneck | Alerts queue up | Reduce parallelism or batch decisions |

---

## 4. Pattern Selection Guide

### 4.1 Decision Matrix

| Scenario | Recommended Pattern | Rationale |
|----------|---------------------|-----------|
| Standard feature | Builder-Reviewer Chain | Simple, effective quality gate |
| Large feature, clear subsystems | Architect + N Builders | Design-first, parallel implementation |
| Security-critical change | Bounded Reviewer Swarm | Multiple specialized perspectives |
| Need specialized research | Sub-Agent Tasks | Delegate without losing context |
| Multi-day project | Session Trees | Preserve context across time |
| Multiple independent features | Multi-Agent Parallel Builders | Maximum velocity |
| Parallel features with potential overlap | Coordination Session | Structured conflict detection |
| Complex with unclear boundaries | Builder-Reviewer Chain | Keep simple until boundaries clear |

### 4.2 When NOT to Use Multi-Agent Patterns

**Use single-agent instead when**:
- Feature is small and well-defined
- Team is new to AOSD (establish basics first)
- Features have unclear boundaries
- Risk of coordination overhead exceeds benefit
- Orchestrator capacity is limited

**Default**: Single agent is sufficient for most work. Multi-agent patterns are for specific situations where the coordination overhead is justified.

---

## 5. Orchestrator Capacity Planning

### 5.1 Solo Orchestrator Limits

| Configuration | Max Agents | Max Slices | Notes |
|---------------|------------|------------|-------|
| Learning AOSD | 1 | 1 | Master basics first |
| Comfortable | 2 | 2 | Builder + Reviewer |
| Proficient | 3-4 | 2-3 | Parallel features possible |
| Expert with tooling | 5-6 | 4-6 | Requires coordination tooling |

### 5.2 Capacity Indicators

**Signs you're at capacity**:
- Missing context switches between agents
- Agents waiting for your input
- Making decisions without full review
- Coordination overhead exceeds implementation time

**Signs you have capacity**:
- Agents complete tasks while you review others
- You can maintain mental model of all active work
- Review quality is not suffering
- Coordination is smooth

---

## 6. Implementation Checklist

Before starting any multi-agent pattern:

**Pre-Flight**:
- [ ] Pattern selected matches scenario
- [ ] Hard limits configured (agents, reviewers, depth)
- [ ] Worktrees created for parallel work
- [ ] Coordination file initialized (if parallel)
- [ ] Trust levels assigned for all agents
- [ ] Rollback plan documented

**During Execution**:
- [ ] All agent spawning initiated by Human Orchestrator
- [ ] No peer-to-peer agent communication
- [ ] Handoffs pass through Human Orchestrator
- [ ] Progress monitored regularly
- [ ] Issues escalated appropriately

**Completion**:
- [ ] All PRs reviewed and merged
- [ ] Worktrees cleaned up
- [ ] Coordination files archived
- [ ] Lessons learned documented
- [ ] Patterns updated if needed

---

## 7. Authority and State Management

When multiple agents work concurrently, **authority ambiguity** becomes the primary source of coordination failures. This section defines how to maintain clear authority over deployable state.

### 7.1 The Authority Principle

> **Only main represents deployable truth.**

This principle must be:
- **Socially understood**: All agents and humans know main is authoritative
- **Procedurally enforced**: Workflows require merging before deployment
- **Mechanically verified**: Pre-deploy checks validate branch ancestry

**Why This Matters**: Multi-agent work doesn't create authority problems—it surfaces latent ones faster. A fix that exists only in a feature branch can be deployed, appear to work, and be silently overwritten by a subsequent deployment from main. The compressed timescales of multi-agent work make this failure mode visible within minutes rather than days.

### 7.2 Authority Boundaries by Role

Extend agent role definitions with explicit authority boundaries:

| Role | Branch Authority | Deploy Authority |
|------|------------------|------------------|
| **Builder/Implementer** | Can create and work in feature branches | Cannot deploy to any environment |
| **Reviewer** | Read-only access to all branches | Cannot deploy |
| **Deploy Agent** (if used) | Read-only; operates only on main | Can deploy only commits reachable from main |
| **Human Orchestrator** | Full authority | Final authority on all deployments |

**Key Constraint**: No agent may deploy from a feature branch, even if tests pass and the fix "works." Deployment authority is derived from merge to main, not from test results.

### 7.3 State Management Patterns

**Pattern: Explicit Merge-Before-Deploy**

```
┌─────────────────────────────────────────────────────────────┐
│                    CORRECT WORKFLOW                         │
├─────────────────────────────────────────────────────────────┤
│  Agent 1 (feature-a)    Agent 2 (feature-b)                 │
│         │                      │                            │
│         ▼                      ▼                            │
│    Implement fix          Implement fix                     │
│         │                      │                            │
│         ▼                      ▼                            │
│    Create PR               Create PR                        │
│         │                      │                            │
│         └──────────┬───────────┘                            │
│                    ▼                                        │
│           Human reviews both                                │
│                    │                                        │
│         ┌─────────┴─────────┐                               │
│         ▼                   ▼                               │
│    Merge PR #1         Merge PR #2                          │
│         │                   │                               │
│         └─────────┬─────────┘                               │
│                   ▼                                         │
│           main now has both                                 │
│                   │                                         │
│                   ▼                                         │
│         Deploy from main ✓                                  │
└─────────────────────────────────────────────────────────────┘
```

**Anti-Pattern: Deploy-Before-Merge**

```
┌─────────────────────────────────────────────────────────────┐
│                    INCORRECT WORKFLOW                       │
├─────────────────────────────────────────────────────────────┤
│  Agent 1 (feature-a)                                        │
│         │                                                   │
│         ▼                                                   │
│    Implement fix                                            │
│         │                                                   │
│         ▼                                                   │
│    Deploy from feature-a  ← VIOLATION                       │
│         │                                                   │
│         ▼                                                   │
│    "Fix works!" (in feature-a environment)                  │
│         │                                                   │
│         ▼                                                   │
│    Agent 2 deploys from main                                │
│         │                                                   │
│         ▼                                                   │
│    Fix is gone ✗ (main didn't have it)                      │
└─────────────────────────────────────────────────────────────┘
```

### 7.4 Pre-Deploy Authority Check

Every deployment pipeline should include:

```bash
# Authority check - MUST pass before any deployment
git fetch origin main
if ! git merge-base --is-ancestor HEAD origin/main; then
    echo "BLOCKED: Cannot deploy - commit not on main"
    echo "Merge your changes to main first"
    exit 1
fi
```

This check converts the Authority Principle from policy to enforcement.

### 7.5 Deployment Provenance

When deployments occur, capture provenance for forensic clarity:

```
Deployment Record:
  Commit: abc1234def5678
  Branch: main
  PR: #XXX
  Agent: builder-1 (or 'human')
  Timestamp: 2025-12-29T14:30:00Z
  Environment: staging
```

If any field is missing or branch ≠ main, the deployment is non-compliant.

### 7.6 Reducing Authority Ambiguity

**Delete feature branches after merge**: Branches that exist imply "maybe this is authoritative." Agents infer from presence unless told otherwise. Auto-deleting merged branches removes this ambiguity.

**Single source of truth**: Never maintain parallel "working" versions. If something needs to be deployed, it must be on main first.

**Explicit handoffs**: When multiple agents work on related changes, Human Orchestrator determines merge order explicitly. Agents do not self-coordinate deployment sequencing.

---

## 8. Resolving Agent Disagreement

When multiple agents reach different conclusions about root cause or approach:

- Disagreement is signal, not failure—different models weight factors differently
- Do not resolve disagreement through debate or model authority
- Trigger measurement: instrument, observe, let data decide
- Measurement overrides all agent hypotheses, including the majority view
- Share findings with all agents so context is updated
- If measurement is impractical, escalate to Human Orchestrator for decision

---

## 9. Human-Mediated Coordination Protocols

This section defines operational protocols for coordinating parallel sessions when agents cannot communicate directly. These protocols complement the patterns in [Section 3](#3-pattern-catalog) and apply especially to the [Coordination Session Pattern](#37-coordination-session-pattern).

### 9.1 Parallel Session Guardrails

These guardrails govern parallel session coordination. They are workflow rules, not executable invariants.

| ID | Guardrail | Rationale |
|----|-----------|-----------|
| **G-01** | Coordination MUST start before parallel work begins | Prevents ownership conflicts from forming |
| **G-02** | Work items MUST be validated before assignment (open, scoped, unblocked) | Prevents wasted effort on stale or blocked items |
| **G-03** | Ownership MUST be declared per session; overlap requires explicit coordination | Clear boundaries prevent silent conflicts |
| **G-04** | Session prompts SHOULD include stable identity and resumability context | Enables handoffs and recovery |
| **G-05** | Deploy authority MUST be explicit; default to single deploy authority | See [Section 7](#7-authority-and-state-management) and [Deployment Authority Invariants](./INVARIANTS_COOKBOOK.md#11-deployment-authority-invariants) |
| **G-06** | Merge order MUST be intentional; default smaller/lower-risk first | Reduces conflict surface; exceptions require explicit reasoning |

**Note**: G-05 and G-06 are enforced as invariants elsewhere. These guardrails reference rather than duplicate that content.

### 9.2 Renegotiation Protocol

When a worker discovers it must touch files outside its declared boundary, or a dependency breaks the initial plan, this protocol applies.

**Trigger Conditions**:
- Worker needs to modify a file owned by another session
- Hidden dependency discovered (shared utility, common config, etc.)
- Scope change invalidates original boundary
- Tests fail due to changes in shared surface
- Architecture assumption proves incorrect

**Required Behavior: STOP-and-Log**

Workers MUST NOT silently violate boundaries. When a trigger condition is detected:

1. **STOP**: Halt work on the conflicting change
2. **LOG**: Document what was discovered and why boundary violation is needed
3. **ALERT**: Emit a Human Alert (see [Section 9.3](#93-human-alert-format))
4. **WAIT**: Do not proceed until human provides resolution

**Decision Tree** (for Human Orchestrator):

```
Boundary violation detected
           │
           ▼
   Is the overlap minor and safe?
           │
     ┌─────┴─────┐
     │ YES       │ NO
     ▼           ▼
 Reassign    Is serialization acceptable?
 ownership        │
 to one      ┌────┴────┐
 session     │ YES     │ NO
             ▼         ▼
         Serialize  Create "seam task"
         the work   (new work item for
         (pause one, shared changes)
         let other
         complete)
```

**Resolution Options**:

| Option | When to Use | Action |
|--------|-------------|--------|
| **Reassign ownership** | Overlap is minor; one session can absorb it | Update ownership declaration; notify both sessions |
| **Create seam task** | Shared change is substantial enough to be its own work item | Create new issue; assign to one session or new session |
| **Serialize work** | Overlap is temporary; can proceed after other session completes | Pause blocked session; resume after merge |
| **Split PRs** | Changes are separable; can merge independently | Each session creates focused PR; coordinate merge order |

**Escalation Criteria** (alert Human Orchestrator immediately):
- Boundary violation is required but resolution is unclear
- Deployment sequencing conflict between sessions
- Risk of losing work (e.g., rebase conflict likely)
- Architectural assumption invalidated
- Two or more sessions blocked on same resource

### 9.3 Human Alert Format

Because agents cannot message each other directly today, all coordination flows through the Human Orchestrator. This format provides a structured, copy/paste-friendly alert block.

**Format**:

```
═══════════════════════════════════════════════════════════
ALERT TO HUMAN
═══════════════════════════════════════════════════════════
FROM: [Session identifier, e.g., "Worker-1 (feature-auth)"]
TYPE: [OVERLAP | MERGE_CONFLICT | SCOPE_CHANGE | BLOCKED | OTHER]

WHAT HAPPENED:
[1-3 sentences describing the issue]

WHY IT MATTERS:
[Impact if not addressed: blocked work, potential conflict, lost effort]

STATE SNAPSHOT:
  Branch: [branch name]
  PR: [PR number or "not yet created"]
  Files touched: [list key files]
  Blocked on: [what's blocking progress]

OPTIONS:
  A) [First option - often the recommended one]
  B) [Second option]
  C) [Third option, if applicable]

RECOMMENDED: [A, B, or C] because [brief rationale]

DECISION NEEDED:
[Specific question requiring human answer]

COPY/PASTE INSTRUCTIONS:
  → If A: Paste this into [session name]: "[exact text]"
  → If B: Paste this into [session name]: "[exact text]"
═══════════════════════════════════════════════════════════
```

**Example 1: Overlap Discovered**

```
═══════════════════════════════════════════════════════════
ALERT TO HUMAN
═══════════════════════════════════════════════════════════
FROM: Worker-2 (feature-notifications)
TYPE: OVERLAP

WHAT HAPPENED:
I need to modify `src/utils/api-client.ts` to add notification
headers, but this file is owned by Worker-1 (feature-auth).

WHY IT MATTERS:
If I modify this file, we'll have merge conflicts. If I don't,
notifications won't include auth context.

STATE SNAPSHOT:
  Branch: feature/notifications
  PR: #142
  Files touched: src/notifications/*, src/utils/api-client.ts (needed)
  Blocked on: api-client.ts ownership

OPTIONS:
  A) Reassign api-client.ts to me; I'll make both changes
  B) Create seam task: "Add header injection hook to api-client"
  C) I wait for Worker-1 to complete; rebase after their merge

RECOMMENDED: B because the hook is a clean abstraction that
both features can use.

DECISION NEEDED:
Should I proceed with option A, B, or C?

COPY/PASTE INSTRUCTIONS:
  → If A: Paste into Worker-1: "api-client.ts reassigned to Worker-2.
    Do not modify this file. Rebase after their merge."
  → If B: Paste into Worker-1: "New seam task created: add header
    injection hook to api-client.ts. You own this. Worker-2 blocked
    until complete."
═══════════════════════════════════════════════════════════
```

**Example 2: Merge/Deploy Sequencing Conflict**

```
═══════════════════════════════════════════════════════════
ALERT TO HUMAN
═══════════════════════════════════════════════════════════
FROM: Coordination Session
TYPE: MERGE_CONFLICT

WHAT HAPPENED:
Worker-1 (PR #140) and Worker-3 (PR #143) both modify the
database migration sequence. Merging in wrong order will
break migrations.

WHY IT MATTERS:
If #143 merges first, migration 042 will reference a column
that doesn't exist until #140's migration 041 runs.

STATE SNAPSHOT:
  PR #140: migration 041 (adds user_preferences table)
  PR #143: migration 042 (adds notification_settings, refs user_preferences)
  Blocked on: merge order decision

OPTIONS:
  A) Merge #140 first, then #143 rebases and merges
  B) Worker-3 removes dependency; migrations become independent
  C) Combine into single PR (loses parallel benefit)

RECOMMENDED: A because the dependency is legitimate and #140
is ready to merge now.

DECISION NEEDED:
Approve merge order: #140 → #143?

COPY/PASTE INSTRUCTIONS:
  → If A: Paste into Worker-3: "Hold merge. #140 merges first.
    Rebase after #140 lands, then proceed."
  → If B: Paste into Worker-3: "Remove dependency on user_preferences
    table. Make migration self-contained."
═══════════════════════════════════════════════════════════
```

### 9.4 Implementation Notes (Non-Normative)

> **Note**: This section contains optional implementation recipes. These are not required by AOSD—they are practical suggestions that may help operationalize the patterns above.

**Session Identity Headers**

When starting worker sessions, consider including a stable identity block:

```
You are Worker-2 in a parallel session coordinated by a human orchestrator.

Session Identity:
  ID: worker-2
  Feature: notifications
  Branch: feature/notifications
  Owned files: src/notifications/**

Coordination Rules:
  - Do NOT modify files outside your ownership without alerting human
  - If you need a file owned by another session, emit ALERT TO HUMAN
  - Human will copy relevant context from other sessions as needed
```

**Git Worktree Usage**

For file-level isolation between parallel sessions:

```bash
# Create isolated worktrees for each worker
git worktree add ../project-worker-1 -b feature/auth
git worktree add ../project-worker-2 -b feature/notifications

# Each session works in its own directory
# Prevents accidental cross-modification
```

**Measuring Coordination Effectiveness**

Rather than claiming specific throughput improvements, measure:
- Time spent blocked waiting for human decisions
- Frequency of renegotiation triggers
- Merge conflict rate compared to sequential work
- Human alert queue depth (are you becoming a bottleneck?)

These metrics help calibrate parallelism level to your actual coordination capacity.

---

## 10. Related Documents

- [AOSD Framework](./AOSD_FRAMEWORK.md) - Core principles (especially Principle 1 and 8)
- [Agent Communication & Protocols](./AGENT_COMMUNICATION.md) - Communication architecture, MCP guidelines, logging requirements
- [Anti-Patterns](./ANTI_PATTERNS.md) - What NOT to do with multi-agent workflows
- [Development Workflow](./DEVELOPMENT_WORKFLOW.md) - Standard workflow phases
- [AI Prompting Patterns](./AI_PROMPTING_PATTERNS.md) - How to prompt agents effectively
- [Module 11: Parallel Development](../SYLLABUS/MODULE_11_PARALLEL_DEVELOPMENT.md) - Detailed parallel development guidance

---

**End of MULTI_AGENT_PATTERNS.md**
