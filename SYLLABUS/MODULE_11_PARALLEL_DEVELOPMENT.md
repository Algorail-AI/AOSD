# Module 11 — Teams, Scaling & Parallel Development
**AOSD Curriculum**
**Scaling AOSD from Solo Developer to Enterprise Teams + Parallel Development Patterns**

---

## 1. Purpose of This Module

This module covers two related topics:

1. **Scaling AOSD** — How AOSD adapts from solo developers to enterprise teams
2. **Parallel Development** — Running multiple AI agent sessions simultaneously for increased velocity

You will learn:

- **Scaling patterns** for different team sizes (solo, small, medium, enterprise)
- Roles and responsibilities at each scale
- Slice distribution and branch ownership strategies
- AI-assisted coordination mechanisms
- When to use parallel development (and when not to)
- Session roles (PRIMARY, SECONDARY, MONITOR)
- Filesystem isolation strategies (git worktrees)
- Resource arbitration and merge strategies

**Reference**: See [Scaling AOSD for Teams and Organizations](../MANUAL/AOSD_FRAMEWORK.md) in the framework for comprehensive guidance.

---

# Part 1: Scaling AOSD for Teams

---

## 2. Solo Developer (Single Orchestrator)

The simplest AOSD configuration: one human orchestrating one or more AI agents.

### Characteristics

| Aspect | Description |
|--------|-------------|
| **Team Size** | 1 developer |
| **AI Agents** | 1-3 agents (typically Builder, optional Reviewer) |
| **Coordination** | None required—single context holder |
| **Branching** | Simple feature branches |
| **Slice Ownership** | All slices owned by the solo developer |

### Best Practices

- **Use AI as your reviewer**: Even solo, the Reviewer agent provides valuable second opinions
- **Maintain documentation discipline**: You are your own future onboarding audience
- **TodoWrite religiously**: Solo developers are most prone to losing context between sessions
- **Commit frequently**: Small, well-documented commits aid future understanding
- **Time-box AI sessions**: Prevent rabbit holes by setting session limits

### Common Pitfalls

| Pitfall | Consequence | Prevention |
|---------|-------------|------------|
| Skipping slice specs | Context loss, feature creep | Always write specs, even brief ones |
| No code review | Quality degradation | Use Reviewer agent consistently |
| Inconsistent documentation | Future confusion | Update docs as part of each slice |

---

## 3. Small Teams (2-5 Developers)

Small teams introduce coordination needs while remaining agile.

### Roles and Responsibilities

**Lead Orchestrator** (often informal):
- Maintains architectural coherence
- Resolves conflicts in approach
- Reviews cross-cutting changes
- Manages shared AI context (prompt templates, invariants)

**Team Members**:
- Own individual slices
- Conduct peer reviews
- Contribute to shared documentation
- Follow established patterns

### Slice Distribution Strategies

| Strategy | Description | Trade-offs |
|----------|-------------|------------|
| **Individual Ownership** | Each developer owns distinct slices | Clear accountability, risk of silos |
| **Pair Ownership** | Two developers share slice responsibility | Built-in review, higher coordination |
| **Rotating Ownership** | Slices rotate between developers | Spreads knowledge, needs good handoffs |

### Branch Ownership

**Guidelines**:
- One owner per branch (avoids conflicts)
- Clear naming conventions
- Short-lived branches (merge within days, not weeks)
- PR review required before merge

### Coordination Patterns

- **Daily Sync** (15 minutes): What slices are in progress, blockers, coordination needs
- **Shared Context**: Common prompt templates, shared invariants document
- **Async Coordination**: Work item comments, PR descriptions, shared documentation

---

## 4. Medium Teams (6-15 Developers)

Medium teams require more formal coordination and may split into sub-teams.

### Structure

Teams may organize around:
- **Feature domains** (e.g., auth team, payments team)
- **Layer specialization** (e.g., frontend team, backend team)
- **Mixed** (cross-functional feature teams)

### Key Practices

- **Architectural review board**: Cross-team coordination for invariant changes
- **Shared prompt library**: Centralized, versioned prompt templates
- **Documentation standards**: Consistent format across all sub-teams
- **Regular sync meetings**: Weekly cross-team coordination

---

## 5. Enterprise Teams (15+ Developers)

Large organizations require formal governance and platform team support.

### Additional Requirements

- **AOSD Platform Team**: Maintains shared tooling, templates, and governance
- **Formal training program**: Structured onboarding using the Syllabus
- **Governance processes**: Change control for invariants and patterns
- **Metrics and reporting**: Track AOSD adoption and effectiveness

### Enterprise Considerations

| Challenge | Solution |
|-----------|----------|
| Consistency across teams | Shared prompt library + automated enforcement |
| Knowledge silos | Rotation programs + documentation standards |
| Onboarding at scale | Self-service training + mentorship programs |
| Governance overhead | Automated checks + clear escalation paths |

---

# Part 2: Parallel Multi-Agent Development

---

## 6. When to Use Parallel Development

**NOTE**: Parallel development is primarily useful for:
- Teams with multiple developers
- Complex projects requiring parallel feature development
- Situations where 2-3x development velocity is needed

**Solo developers** can typically skip this section unless running multiple Claude Code sessions simultaneously.

### 6.1 Safe Scenarios (Parallel Development Recommended)
✅ **Independent features**
- Feature A: Add export functionality
- Feature B: Add notification system
- No shared code, different Lambdas

✅ **Different layers**
- Session 1: Frontend changes
- Session 2: Backend Lambda changes
- Minimal overlap

✅ **Bug fixes in separate modules**
- Session 1: Fix auth bug
- Session 2: Fix rendering bug
- Different files

### 6.2 Unsafe Scenarios (Serial Development Required)
❌ **Architectural changes**
- Changing core patterns
- Modifying invariants
- Refactoring shared code
- **Reason**: Too risky to parallelize

❌ **Same files/modules**
- Both sessions editing same Lambda
- Both modifying same DDB table schema
- **Reason**: Merge conflicts guaranteed

❌ **Dependent features**
- Feature B requires Feature A to be complete
- **Reason**: Dependency chain breaks parallel execution

❌ **Single-point bottlenecks**
- Both sessions need to deploy simultaneously
- Both sessions running slow tests
- **Reason**: Resource contention

### 6.3 Decision Matrix
| Scenario | Parallel? | Rationale |
|----------|-----------|-----------|
| 2 new Lambdas, no shared code | ✅ Yes | Independent |
| 2 bug fixes, different modules | ✅ Yes | No overlap |
| Frontend + Backend for same feature | ⚠️  Maybe | Coordinate interfaces |
| Refactor + new feature | ❌ No | Refactor affects new feature |
| 2 architectural changes | ❌ No | Too much risk |

---

## 7. Session Roles

### 7.1 PRIMARY Session
**Responsibilities**:
- Deploys to Dev
- Runs slow tests
- Merges first
- Highest priority for resources

**Typical use**: Most complex or riskiest feature

**Branch**: `feature/primary-feature`

### 7.2 SECONDARY Session
**Responsibilities**:
- Works in isolation
- Cannot deploy until PRIMARY completes
- Merges after PRIMARY
- Must rebase on PRIMARY's changes

**Typical use**: Independent feature with lower complexity

**Branch**: `feature/secondary-feature`

### 7.3 MONITOR Role (Human)
**Responsibilities**:
- Reviews coordination file
- Arbitrates resource conflicts
- Approves merges
- Watches for failures

**Frequency**: Check every 30-60 minutes

---

## 8. Filesystem Isolation with Git Worktrees

### 8.1 Why Worktrees?
**Problem**: Running 2 Claude Code sessions in same directory causes file conflicts.

**Solution**: Git worktrees create separate filesystem copies.

**Benefits**:
- Each session has own working directory
- No file conflicts during editing
- Both sessions share same git repo
- Easy cleanup

### 8.2 Setup Workflow
```bash
# Main directory (PRIMARY session)
cd /path/to/repo
git checkout -b feature/primary-feature

# Create worktree for SECONDARY session
git worktree add ../repo-secondary feature/secondary-feature

# Now you have:
# /path/to/repo (PRIMARY)
# /path/to/repo-secondary (SECONDARY)
```

### 8.3 Session Startup
**PRIMARY** (terminal 1):
```bash
cd /path/to/repo
# Start Claude Code session 1
```

**SECONDARY** (terminal 2):
```bash
cd /path/to/repo-secondary
# Start Claude Code session 2
```

### 8.4 Cleanup
```bash
# After parallel dev complete
git worktree remove ../repo-secondary
```

---

## 9. Coordination File Protocol

### 9.1 Purpose
Sessions communicate via `PARALLEL_DEV_COORDINATION.md` file.

### 9.2 Format
```markdown
# Parallel Development Coordination

## PRIMARY Session
**Status**: IN_PROGRESS / TESTING / COMPLETE
**Feature**: [Description]
**Branch**: feature/primary-feature
**ETA**: [Estimated completion]
**Resources Needed**: deployment, slow tests
**Last Updated**: 2025-01-15 14:30

## SECONDARY Session
**Status**: IN_PROGRESS / WAITING / COMPLETE
**Feature**: [Description]
**Branch**: feature/secondary-feature
**Dependencies**: Waiting for PRIMARY deployment to complete
**Resources Needed**: None (self-contained)
**Last Updated**: 2025-01-15 14:25

## Resource Status
**Deployment**: LOCKED by PRIMARY
**Slow Tests**: AVAILABLE
**Dev Environment**: LOCKED by PRIMARY

## Notes
- SECONDARY must rebase on PRIMARY before merge
- PRIMARY deploying to Dev at 14:30
- SECONDARY can test locally until deployment available
```

### 9.3 Update Frequency
- Update after each major task completion
- Before requesting resources (deployment, tests)
- When blocked or waiting

---

## 10. Resource Arbitration

### 10.1 Shared Resources
| Resource | Contention | Strategy |
|----------|------------|----------|
| **Dev Deployment** | HIGH | PRIMARY only |
| **Slow Tests** | MEDIUM | Sequential |
| **CPU** | LOW | Monitor load |
| **DynamoDB (Dev)** | HIGH | PRIMARY only deploys tables |

### 10.2 Deployment Lock
**Rule**: Only PRIMARY can deploy to Dev during parallel development.

**Rationale**: Deploying from multiple branches causes unpredictable state.

**SECONDARY must**:
- Work without deployment
- Test locally where possible
- Wait for PRIMARY to complete
- Deploy only after merging PRIMARY's changes

### 10.3 Test Execution
**Fast tests**: Both sessions can run simultaneously (separate filesystems)

**Medium tests**: Both sessions can run simultaneously (mocked AWS)

**Slow tests**: Run sequentially
- PRIMARY runs first
- SECONDARY waits
- Coordinate via PARALLEL_DEV_COORDINATION.md

### 10.4 CPU/Memory
**Monitor**: Run `htop` or Activity Monitor

**If overloaded**:
- Pause one session
- Let other session complete critical task
- Resume when resources available

---

## 11. Merge Strategy

### 11.1 Merge Order
**ALWAYS**: SECONDARY merges AFTER PRIMARY

**Workflow**:
1. PRIMARY completes feature → merges to main
2. SECONDARY rebases on main
3. SECONDARY resolves any conflicts
4. SECONDARY completes feature → merges to main

### 11.2 PRIMARY Merge
```bash
# PRIMARY session
git checkout main
git pull origin main
git merge feature/primary-feature
git push origin main
```

### 11.3 SECONDARY Rebase & Merge
```bash
# SECONDARY session
git checkout main
git pull origin main  # Gets PRIMARY's changes
git checkout feature/secondary-feature
git rebase main  # Rebase on PRIMARY's work
# Resolve conflicts if any
git checkout main
git merge feature/secondary-feature
git push origin main
```

### 11.4 Conflict Resolution
**If conflicts occur during rebase**:
1. Review conflicting files
2. Resolve manually (prefer PRIMARY's changes unless SECONDARY has good reason)
3. Run all tests after resolution
4. Complete rebase
5. Merge to main

---

## 12. Failure Modes & Recovery

### 12.1 Common Failure Modes

**Failure Mode 1: Both Sessions Deploy Simultaneously**
- **Symptom**: Dev environment in undefined state
- **Recovery**:
  1. Identify which deployment succeeded
  2. Rollback other session's changes
  3. Re-deploy PRIMARY's version
  4. SECONDARY rebases and redeploys later

**Failure Mode 2: Merge Conflicts**
- **Symptom**: Git rebase fails with conflicts
- **Recovery**:
  1. Review conflict markers
  2. Understand both changes
  3. Resolve carefully (prefer PRIMARY unless clear reason)
  4. Run full test suite
  5. Complete merge

**Failure Mode 3: Resource Starvation**
- **Symptom**: System becomes unresponsive, sessions slow
- **Recovery**:
  1. Pause SECONDARY session
  2. Let PRIMARY complete critical task
  3. Resume SECONDARY when resources available
  4. Consider staggering sessions in future

**Failure Mode 4: Coordination File Out of Sync**
- **Symptom**: Both sessions claim same resource
- **Recovery**:
  1. Human MONITOR arbitrates
  2. Update coordination file with truth
  3. One session yields resource
  4. Resume with clear ownership

### 12.2 Failure Log
Keep a `PARALLEL_DEV_FAILURE_LOG.md` to document issues:

```markdown
## Failure: Deployment Collision

**Date**: 2025-01-15
**Sessions**: PRIMARY (feature/auth), SECONDARY (feature/export)
**What Happened**: Both sessions deployed to Dev within 2 minutes
**Impact**: Dev environment had mixed code, tests failed
**Root Cause**: SECONDARY didn't check coordination file before deploying
**Recovery**: Rollback to PRIMARY's deployment, SECONDARY rebased and redeployed later
**Prevention**: Enforce coordination file update before ANY deployment
```

---

## 13. CI/CD Integration

### 13.1 Branch Protection
**For parallel development, configure**:
- Require status checks before merge
- Require up-to-date branches
- Both PRIMARY and SECONDARY must pass CI

### 13.2 CI Workflow
```yaml
# .github/workflows/parallel-dev-ci.yml

name: Parallel Development CI

on:
  push:
    branches:
      - 'feature/**'

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run Fast Tests
        run: make test-fast
      - name: Run Medium Tests
        run: make test-medium
      # Slow tests only for PRIMARY
      - name: Run Slow Tests (if PRIMARY)
        if: github.ref == 'refs/heads/feature/primary-feature'
        run: make test-slow
```

### 13.3 Deployment from CI
**Rule**: CI never deploys during parallel development.

**Rationale**: Deployment must be coordinated via PRIMARY session.

---

## 14. Worked Example

**Scenario**: Build two independent features simultaneously
- PRIMARY: Add email notifications
- SECONDARY: Add PDF export

### 14.1 Setup
```bash
# PRIMARY
cd /path/to/your-project
git checkout -b feature/email-notifications
echo "## PRIMARY Session: Email Notifications" > PARALLEL_DEV_COORDINATION.md

# SECONDARY
git worktree add ../your-project-secondary feature/pdf-export
cd ../your-project-secondary
# Update coordination file
```

### 14.2 Development Phase
**PRIMARY** (9:00 AM):
- Implements email notification Lambda
- Writes tests
- Updates coordination file: "Status: TESTING, Need deployment at 10:00"

**SECONDARY** (9:00 AM):
- Implements PDF export Lambda
- Writes tests (local only, no deployment needed yet)
- Updates coordination file: "Status: IN_PROGRESS, No deployment needed"

### 14.3 Resource Coordination
**PRIMARY** (10:00 AM):
- Locks deployment in coordination file
- Deploys to Dev
- Runs slow tests
- Updates: "Status: COMPLETE"

**SECONDARY** (10:15 AM):
- Sees PRIMARY complete
- Continues development
- Completes feature by 11:00
- Updates: "Status: READY_TO_MERGE, Waiting for PRIMARY merge"

### 14.4 Merge Phase
**PRIMARY** (11:00 AM):
```bash
git checkout main
git merge feature/email-notifications
git push origin main
```

**SECONDARY** (11:05 AM):
```bash
git checkout main
git pull origin main  # Gets PRIMARY's changes
git checkout feature/pdf-export
git rebase main  # No conflicts - independent features
git checkout main
git merge feature/pdf-export
git push origin main
```

### 14.5 Cleanup
```bash
git worktree remove ../your-project-secondary
rm PARALLEL_DEV_COORDINATION.md
```

**Result**: 2 features delivered in same time as 1 would have taken serially.

---

## 15. Hands-On Exercises

### Exercise 1 — Parallel Dev Scenario Analysis
Given these scenarios, determine if parallel development is safe:

1. Add new Lambda for user management + Add new Lambda for reporting
2. Refactor AWS Factory Pattern + Add new feature using factory
3. Fix bug in auth handler + Fix bug in export handler
4. Add frontend form + Add backend API for same form

For each, answer:
- Safe for parallel dev? (Yes/No/Maybe)
- What are the risks?
- How would you mitigate risks?

### Exercise 2 — Coordination File Practice
Draft a `PARALLEL_DEV_COORDINATION.md` for this scenario:
- PRIMARY: Implementing SSO integration (complex, needs deployment)
- SECONDARY: Adding CSV export (simple, local testing)

Include:
- Status for both sessions
- Resource locks
- Dependencies
- ETAs

### Exercise 3 — Failure Recovery
You're the MONITOR. Both sessions deployed to Dev simultaneously, and now the environment is in an undefined state.

Draft a recovery plan:
1. Immediate actions
2. How to determine correct state
3. Rollback steps
4. Prevention for future

### Exercise 4 — Merge Conflict Resolution
Simulate a merge conflict scenario (on paper):
- PRIMARY added `get_user_email()` function
- SECONDARY added `get_user_phone()` function in same file
- Both modified imports list

How would you resolve the rebase conflict?

---

## 16. Completion Criteria

You've mastered this module when you can:

**Part 1: Scaling**
- Understand and apply AOSD patterns for each team size (solo, small, medium, enterprise)
- Define roles and responsibilities appropriate to team scale
- Implement slice distribution and branch ownership strategies
- Establish AI-assisted coordination mechanisms

**Part 2: Parallel Development**
- Distinguish safe vs unsafe scenarios for parallel development
- Set up git worktrees for filesystem isolation
- Assign PRIMARY and SECONDARY session roles
- Use coordination file protocol effectively
- Arbitrate resource conflicts (deployment, tests, CPU)
- Execute correct merge strategy (SECONDARY after PRIMARY)
- Recognize and recover from common failure modes
- Balance velocity gains against coordination overhead

**Key insight**: AOSD scales from solo to enterprise, but the coordination mechanisms must evolve with team size. Parallel development is a velocity multiplier that requires additional discipline.

---

## 17. When NOT to Use Parallel Development

Even if technically possible, skip parallel development when:

1. **Solo founder with simple features**: Overhead not worth it
2. **High interdependency**: Features depend on each other
3. **Learning phase**: Still mastering AOSD, don't add complexity
4. **Architectural changes**: Too risky to parallelize
5. **Tight deadline with no margin**: Coordination overhead could slow you down
6. **First time in new codebase**: Need to understand architecture first

**Default**: Serial development. **Exception**: Parallel when clear benefit and low risk.

---

**This concludes MODULE_11**

**Congratulations!** You've completed all 11 modules of the AOSD curriculum. You now have comprehensive knowledge of AOSD, from foundations through team scaling and parallel development.

**Next steps**:
- Apply patterns in real projects
- Contribute to framework evolution
- Share learnings within the team
- Continue refining through practice

---

**End of MODULE_11_PARALLEL_DEVELOPMENT.md**
