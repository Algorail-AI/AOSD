# Parallel Development Patterns

**Running Multiple AI Agent Sessions Simultaneously in AOSD**

---

## 1. Purpose

This document defines **parallel development patterns**—techniques for running multiple AI agent sessions simultaneously to increase development velocity while maintaining AOSD safety guarantees.

**When to Use Parallel Development**:
- Multiple independent features needed simultaneously
- Team members working on different slices
- Velocity is critical and features are well-defined

**When NOT to Use**:
- Learning AOSD (master basics first)
- Features have dependencies on each other
- Working on architectural changes
- Unclear feature boundaries

**Default**: Serial development. Use parallel development only when benefits clearly outweigh coordination overhead.

---

## 2. Core Concepts

### 2.1 Filesystem Isolation with Git Worktrees

**Problem**: Running multiple Claude Code sessions in the same directory causes file conflicts.

**Solution**: Git worktrees create separate filesystem copies while sharing the same repository.

**Setup**:
```bash
# Main directory (PRIMARY session)
cd /path/to/repo
git checkout -b feature/primary-feature

# Create worktree for SECONDARY session
git worktree add ../repo-secondary feature/secondary-feature

# Result:
# /path/to/repo (PRIMARY session workspace)
# /path/to/repo-secondary (SECONDARY session workspace)
```

**Benefits**:
- Each session has its own working directory
- No file conflicts during editing
- Both sessions share same git repository
- Easy to clean up when done

**Cleanup**:
```bash
git worktree remove ../repo-secondary
```

### 2.2 Session Roles

Every parallel development session has a designated role:

| Role | Responsibilities | Resource Access |
|------|------------------|-----------------|
| **PRIMARY** | Deploys to dev, runs slow tests, merges first | Highest priority |
| **SECONDARY** | Works in isolation, merges after PRIMARY | Waits for PRIMARY |
| **MONITOR** (Human) | Reviews coordination, arbitrates conflicts | Oversight |

**PRIMARY Session**:
- Most complex or riskiest feature
- Has deployment lock
- Merges to main first
- Other sessions rebase on its changes

**SECONDARY Session**:
- Independent feature with lower complexity
- Cannot deploy until PRIMARY completes
- Must rebase on PRIMARY before merging
- Works without blocking resources

### 2.3 Orchestrator Capacity Limits

Human orchestrators have cognitive limits for managing parallel work:

| Configuration | Max Parallel Slices | Notes |
|---------------|---------------------|-------|
| **Learning AOSD** | 1 | Master basics first |
| **Solo, comfortable** | 2-4 | Standard parallel development |
| **Solo with tooling** | 6-8 | Requires coordination tooling |
| **Team with tooling** | Scale with team | Each member owns their slices |

**Signs You're Over Capacity**:
- Missing context switches between sessions
- Sessions waiting for your input
- Making decisions without full review
- Coordination overhead exceeds implementation time

**Signs You Have Capacity**:
- Sessions complete tasks while you review others
- You maintain mental model of all active work
- Review quality is not suffering

---

## 3. Safe vs Unsafe Scenarios

### 3.1 Safe for Parallel Development

**Independent Features**:
```
Session 1: Add export functionality (exports/)
Session 2: Add notification system (notifications/)
No shared code, different modules
```

**Different Layers**:
```
Session 1: Frontend changes (src/ui/)
Session 2: Backend Lambda changes (src/handlers/)
Minimal overlap
```

**Bug Fixes in Separate Modules**:
```
Session 1: Fix auth bug (src/auth/)
Session 2: Fix rendering bug (src/ui/)
Different files
```

### 3.2 Unsafe for Parallel Development

**Architectural Changes**:
- Changing core patterns
- Modifying invariants
- Refactoring shared code
- **Reason**: Too risky to parallelize

**Same Files/Modules**:
- Both sessions editing same handler
- Both modifying same database schema
- **Reason**: Merge conflicts guaranteed

**Dependent Features**:
- Feature B requires Feature A to be complete
- **Reason**: Dependency chain breaks parallel execution

**Single-Point Bottlenecks**:
- Both sessions need to deploy simultaneously
- Both sessions running slow tests
- **Reason**: Resource contention

### 3.3 Decision Matrix

| Scenario | Parallel? | Rationale |
|----------|-----------|-----------|
| 2 new handlers, no shared code | Yes | Independent |
| 2 bug fixes, different modules | Yes | No overlap |
| Frontend + Backend for same feature | Maybe | Coordinate interfaces first |
| Refactor + new feature | No | Refactor affects new feature |
| 2 architectural changes | No | Too much risk |
| Any work touching invariants | No | Requires focused attention |

---

## 4. Coordination Protocol

### 4.1 Coordination File

Sessions communicate via a coordination file that tracks status and resource locks:

```markdown
# Parallel Development Coordination

## PRIMARY Session
**Status**: IN_PROGRESS | TESTING | COMPLETE
**Feature**: [Description]
**Branch**: feature/primary-feature
**Resources Needed**: deployment, slow tests
**Last Updated**: 2025-01-15 14:30

## SECONDARY Session
**Status**: IN_PROGRESS | WAITING | COMPLETE
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
```

**Update Frequency**:
- After each major task completion
- Before requesting resources
- When blocked or waiting

### 4.2 Resource Arbitration

| Resource | Contention Level | Strategy |
|----------|------------------|----------|
| **Dev Deployment** | HIGH | PRIMARY only |
| **Slow Tests** | MEDIUM | Sequential execution |
| **Fast/Medium Tests** | LOW | Both can run (isolated filesystems) |
| **Database Schema Changes** | HIGH | PRIMARY only |

**Deployment Lock Rule**: Only PRIMARY deploys during parallel development.

**Rationale**: Deploying from multiple branches creates unpredictable environment state.

**SECONDARY Must**:
- Work without deployment
- Test locally where possible
- Wait for PRIMARY to complete
- Deploy only after merging PRIMARY's changes

---

## 5. Merge Strategy

### 5.1 Merge Order

**Rule**: SECONDARY merges AFTER PRIMARY.

**Workflow**:
1. PRIMARY completes feature → merges to main
2. SECONDARY pulls main (gets PRIMARY's changes)
3. SECONDARY rebases on main
4. SECONDARY resolves any conflicts
5. SECONDARY merges to main

### 5.2 PRIMARY Merge

```bash
# PRIMARY session
git checkout main
git pull origin main
git merge feature/primary-feature
git push origin main
```

### 5.3 SECONDARY Rebase and Merge

```bash
# SECONDARY session
git checkout main
git pull origin main  # Gets PRIMARY's changes
git checkout feature/secondary-feature
git rebase main       # Rebase on PRIMARY's work
# Resolve conflicts if any
git checkout main
git merge feature/secondary-feature
git push origin main
```

### 5.4 Conflict Resolution

If conflicts occur during rebase:
1. Review conflicting files
2. Resolve manually (prefer PRIMARY's changes unless SECONDARY has good reason)
3. Run all tests after resolution
4. Complete rebase
5. Merge to main

---

## 6. Failure Modes and Recovery

### 6.1 Both Sessions Deploy Simultaneously

**Symptom**: Dev environment in undefined state

**Recovery**:
1. Identify which deployment succeeded
2. Rollback other session's changes
3. Re-deploy PRIMARY's version
4. SECONDARY rebases and redeploys later

**Prevention**: Enforce deployment lock via coordination file

### 6.2 Merge Conflicts

**Symptom**: Git rebase fails with conflicts

**Recovery**:
1. Review conflict markers
2. Understand both changes
3. Resolve carefully (prefer PRIMARY unless clear reason)
4. Run full test suite
5. Complete merge

**Prevention**: Ensure clear feature boundaries before starting

### 6.3 Resource Starvation

**Symptom**: System unresponsive, sessions slow

**Recovery**:
1. Pause SECONDARY session
2. Let PRIMARY complete critical task
3. Resume SECONDARY when resources available

**Prevention**: Monitor system resources, stagger sessions if needed

### 6.4 Coordination File Out of Sync

**Symptom**: Both sessions claim same resource

**Recovery**:
1. Human MONITOR arbitrates
2. Update coordination file with truth
3. One session yields resource
4. Resume with clear ownership

**Prevention**: Update coordination file before any resource claim

---

## 7. Session Lifecycle

### 7.1 Starting Parallel Development

1. **Identify independent features**: Confirm no shared code
2. **Create worktrees**: Set up isolated filesystems
3. **Initialize coordination file**: Document sessions and resources
4. **Assign roles**: Designate PRIMARY and SECONDARY
5. **Start sessions**: Launch AI agents in their worktrees

### 7.2 During Development

1. **Update coordination file**: After each major milestone
2. **Respect resource locks**: Wait for resources, don't grab
3. **Monitor progress**: Human checks every 30-60 minutes
4. **Handle blockers**: Escalate to human if stuck

### 7.3 Completing Parallel Development

1. **PRIMARY merges first**: Complete and merge PRIMARY
2. **SECONDARY rebases**: Pull PRIMARY's changes, rebase
3. **SECONDARY merges**: Complete and merge SECONDARY
4. **Cleanup**: Remove worktrees, archive coordination file
5. **Retrospective**: Document what worked, what didn't

---

## 8. Integration with Multi-Agent Patterns

Parallel development can be combined with multi-agent patterns:

| Pattern | Parallel Integration |
|---------|---------------------|
| **Builder-Reviewer Chain** | Each parallel session has its own builder-reviewer pair |
| **Architect + N Builders** | Architect in PRIMARY, Builders can be distributed across sessions |
| **Bounded Reviewer Swarm** | Reviewers can review across sessions, but sequentially |

**Key Constraint**: Even in parallel development, all multi-agent patterns require Human Orchestrator initiation and coordination.

---

## 9. Worked Example

**Scenario**: Build two independent features simultaneously

- PRIMARY: Add email notifications (`feature/email-notifications`)
- SECONDARY: Add PDF export (`feature/pdf-export`)

### Setup (30 minutes)
```bash
# PRIMARY
cd /path/to/project
git checkout -b feature/email-notifications
echo "## PRIMARY: Email Notifications" > PARALLEL_DEV.md

# SECONDARY
git worktree add ../project-secondary feature/pdf-export
cd ../project-secondary
# Update coordination file
```

### Development Phase (3-4 hours)

**9:00 AM - PRIMARY**:
- Implements email notification Lambda
- Writes tests
- Updates coordination: "Status: TESTING, Need deployment at 10:00"

**9:00 AM - SECONDARY**:
- Implements PDF export Lambda
- Writes tests (local only)
- Updates coordination: "Status: IN_PROGRESS, No deployment needed"

**10:00 AM - PRIMARY**:
- Locks deployment
- Deploys to Dev
- Runs slow tests
- Updates: "Status: COMPLETE"

**10:15 AM - SECONDARY**:
- Sees PRIMARY complete
- Continues development
- Completes by 11:00
- Updates: "Status: READY_TO_MERGE"

### Merge Phase (30 minutes)

**11:00 AM - PRIMARY**:
```bash
git checkout main && git merge feature/email-notifications && git push
```

**11:05 AM - SECONDARY**:
```bash
git checkout main && git pull origin main
git checkout feature/pdf-export && git rebase main
git checkout main && git merge feature/pdf-export && git push
```

### Cleanup
```bash
git worktree remove ../project-secondary
rm PARALLEL_DEV.md
```

**Result**: 2 features delivered in time similar to 1 serial feature.

---

## 10. Checklist

### Pre-Flight
- [ ] Features confirmed independent (no shared code)
- [ ] Worktrees created for isolation
- [ ] Coordination file initialized
- [ ] PRIMARY/SECONDARY roles assigned
- [ ] Human orchestrator capacity available

### During Execution
- [ ] Coordination file updated regularly
- [ ] Resource locks respected
- [ ] Human monitoring every 30-60 minutes
- [ ] Blockers escalated promptly

### Completion
- [ ] PRIMARY merged first
- [ ] SECONDARY rebased and merged
- [ ] Worktrees cleaned up
- [ ] Coordination file archived
- [ ] Lessons documented

---

## 11. Related Documents

- [Development Workflow](./DEVELOPMENT_WORKFLOW.md) - Standard workflow phases
- [Multi-Agent Patterns](./MULTI_AGENT_PATTERNS.md) - Safe multi-agent orchestration patterns
- [Anti-Patterns](./ANTI_PATTERNS.md) - Shared environment anti-pattern
- [Module 11: Teams, Scaling & Parallel Development](../SYLLABUS/MODULE_11_PARALLEL_DEVELOPMENT.md) - Detailed training on parallel development

---

**End of PARALLEL_DEVELOPMENT.md**
