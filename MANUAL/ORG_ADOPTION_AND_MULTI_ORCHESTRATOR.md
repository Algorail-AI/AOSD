# Organization-Level Adoption and Multi-Orchestrator Patterns

**AOSD Framework - Scaling AOSD Across Teams and Organizations**

This document provides guidance for organizations adopting AOSD across multiple teams, projects, and orchestrators.

---

## 1. Purpose

When AOSD moves beyond a single developer or small team, new challenges emerge:
- Multiple orchestrators working in parallel
- Shared knowledge and patterns across teams
- Consistent governance without bureaucracy
- Smooth handoffs between orchestrators

This guide addresses these challenges with:
- Orchestrator role hierarchy
- Multi-orchestrator governance patterns
- Phased adoption model
- Handoff protocols
- Shared knowledge management

---

## 2. Orchestrator Role Hierarchy

### 2.1 Role Definitions

AOSD organizations may have multiple types of orchestrators:

| Role | Scope | Authority | Typical Count |
|------|-------|-----------|---------------|
| **Primary Orchestrator** | Organization or large domain | Sets org-wide patterns, resolves cross-team conflicts | 1-3 per org |
| **Local Orchestrator** | Team or project | Manages day-to-day AI work within their scope | 1 per team/project |
| **Human Operator** | Specific tasks or sessions | Executes within established patterns | Multiple per team |

### 2.2 Primary Orchestrator

The Primary Orchestrator is responsible for organization-wide AOSD governance.

**Responsibilities**:
- Define and maintain the Level 2 Orchestration Profile
- Establish organization-wide invariants
- Resolve cross-team conflicts
- Approve significant pattern changes
- Guide AOSD maturity advancement
- Train and mentor Local Orchestrators

**Authority**:
- Final say on L2 profile changes
- Can override Local Orchestrator decisions (with documentation)
- Sets model governance policies (see [MODEL_GOVERNANCE.md](./MODEL_GOVERNANCE.md))
- Approves new orchestrator onboarding

**Requirements**:
- Deep understanding of AOSD principles
- Technical credibility across teams
- Authority to make binding decisions
- Time allocation for governance work

### 2.3 Local Orchestrator

Local Orchestrators manage AOSD within their team or project.

**Responsibilities**:
- Apply L2 profile to their project(s)
- Create and maintain L3 documentation
- Direct AI agents for daily work
- Report issues to Primary Orchestrator
- Ensure team follows established patterns

**Authority**:
- Full authority within L3 scope
- Can propose L2 changes
- Can escalate to Primary Orchestrator
- Cannot override L2 patterns without approval

**Requirements**:
- Understanding of AOSD fundamentals
- Familiarity with team's technology stack
- Access to AI agents used by team
- Regular communication with Primary Orchestrator

### 2.4 Human Operator

Human Operators execute specific AI-assisted tasks under orchestrator guidance.

**Responsibilities**:
- Execute tasks as directed
- Follow established patterns
- Report anomalies or issues
- Document work for handoffs

**Authority**:
- Execute within defined scope
- Flag issues for escalation
- Cannot modify invariants or patterns
- Cannot approve own work (requires orchestrator review)

**Requirements**:
- Basic AOSD training
- Access to necessary tools
- Clear task assignment

### 2.5 Role Hierarchy Diagram

```
┌─────────────────────────────────┐
│       Primary Orchestrator       │
│    (Org-wide L2 governance)     │
└────────────────┬────────────────┘
                 │
     ┌───────────┼───────────┐
     │           │           │
     ▼           ▼           ▼
┌─────────┐ ┌─────────┐ ┌─────────┐
│  Local  │ │  Local  │ │  Local  │
│ Orch A  │ │ Orch B  │ │ Orch C  │
│(Team A) │ │(Team B) │ │(Team C) │
└────┬────┘ └────┬────┘ └────┬────┘
     │           │           │
     ▼           ▼           ▼
[Operators]  [Operators]  [Operators]
```

---

## 3. Multi-Orchestrator Governance

### 3.1 Authority Boundaries

Clear boundaries prevent conflicts and ensure consistent governance.

| Decision Type | Authority | Escalation Path |
|---------------|-----------|-----------------|
| L2 profile changes | Primary Orchestrator | N/A |
| L3 invariant additions | Local Orchestrator | Primary if cross-team impact |
| Pattern exceptions | Local (document) → Primary (approve) | Required for L2 exceptions |
| Model version changes | Primary Orchestrator | See MODEL_GOVERNANCE.md |
| Cross-team coordination | Primary Orchestrator | Executive if unresolved |
| Emergency fixes | Any orchestrator | Document + notify Primary |

### 3.2 Sync Frequency

Orchestrators must synchronize to prevent drift:

| Sync Type | Frequency | Participants | Purpose |
|-----------|-----------|--------------|---------|
| **Daily standup** | Daily | Local Orchestrators | Status, blockers, handoffs |
| **Weekly sync** | Weekly | Primary + Local Orchestrators | Pattern alignment, issues |
| **Monthly review** | Monthly | All orchestrators | Maturity assessment, L2 updates |
| **Quarterly planning** | Quarterly | Primary + stakeholders | Roadmap, adoption expansion |

### 3.3 Conflict Resolution

When orchestrators disagree, follow this escalation:

```
1. Discussion between affected orchestrators
   ↓ (unresolved)
2. Primary Orchestrator decision
   ↓ (Primary involved in conflict)
3. Documented vote among orchestrators
   ↓ (still unresolved)
4. Executive sponsor decision
```

**Conflict Resolution Principles**:
- Safety wins over convenience
- L1 principles are non-negotiable
- Document all resolutions as ADRs
- Losing party must support decision

### 3.4 Communication Channels

| Channel | Use For | Participants |
|---------|---------|--------------|
| **Sync meetings** | Real-time coordination | Orchestrators |
| **Shared channel** | Async questions, updates | All AOSD practitioners |
| **ADR repository** | Decisions and rationale | All (read), Orchestrators (write) |
| **Escalation path** | Urgent issues | Defined escalation chain |

---

## 4. Phased Adoption Model

### 4.1 Phase 0: Awareness

**Goal**: Understand AOSD and assess fit

**Activities**:
- Study AOSD Framework (Level 1)
- Identify pilot project
- Assess current AI usage patterns
- Identify potential Primary Orchestrator

**Exit Criteria**:
- [ ] Core team has read AOSD_FRAMEWORK.md
- [ ] Pilot project selected
- [ ] Primary Orchestrator candidate identified
- [ ] Executive sponsor engaged

**Duration**: 1-2 weeks

### 4.2 Phase 1: Pilot

**Goal**: Validate AOSD with single team

**Activities**:
- Create Level 2 Orchestration Profile (minimal)
- Implement AOSD on pilot project
- Primary Orchestrator develops expertise
- Document lessons learned

**Exit Criteria**:
- [ ] L2 profile exists (even if minimal)
- [ ] One project using AOSD successfully
- [ ] At least 5 slices completed
- [ ] Primary Orchestrator trained
- [ ] Lessons learned documented

**Duration**: 4-8 weeks

### 4.3 Phase 2: Expansion

**Goal**: Extend AOSD to additional teams

**Activities**:
- Refine L2 profile based on pilot learnings
- Train Local Orchestrators
- Onboard 2-4 additional teams
- Establish sync cadence
- Formalize governance

**Exit Criteria**:
- [ ] L2 profile refined and documented
- [ ] 3-5 teams using AOSD
- [ ] Local Orchestrators trained and active
- [ ] Weekly sync meetings established
- [ ] Cross-team patterns emerging

**Duration**: 2-3 months

### 4.4 Phase 3: Org-Wide Governance

**Goal**: AOSD as standard practice

**Activities**:
- Standardize across all applicable teams
- Formalize training program
- Integrate with existing processes (CI/CD, reviews)
- Continuous improvement cycle

**Exit Criteria**:
- [ ] All applicable teams using AOSD
- [ ] Onboarding process for new orchestrators
- [ ] L2 profile versioned and maintained
- [ ] Metrics tracking (cost, quality, velocity)
- [ ] Regular maturity assessments

**Duration**: Ongoing

### 4.5 Phase Summary

```
Phase 0       Phase 1       Phase 2        Phase 3
Awareness  →   Pilot    →  Expansion   →  Governance

[Learn]     [Validate]   [Scale]        [Standardize]
1-2 weeks   4-8 weeks    2-3 months     Ongoing
```

---

## 5. Orchestrator Handoff Pattern

When orchestrator responsibilities transfer (vacation, role change, departure), proper handoff ensures continuity.

*See also: [TEMPLATES/ORCHESTRATOR_HANDOFF.md](../TEMPLATES/ORCHESTRATOR_HANDOFF.md) for the handoff template.*

### 5.1 Handoff Triggers

| Trigger | Urgency | Handoff Type |
|---------|---------|--------------|
| Planned absence (vacation) | Low | Temporary delegation |
| Role change | Medium | Full transfer |
| Departure | High | Full transfer + knowledge capture |
| Project completion | Low | Archive and close |
| Emergency | Critical | Emergency delegation |

### 5.2 Handoff Checklist

**Knowledge Transfer**:
- [ ] L2/L3 documentation reviewed with successor
- [ ] Open slices and work items explained
- [ ] Pending decisions documented
- [ ] Key relationships introduced

**Access Transfer**:
- [ ] AI agent access granted
- [ ] Repository access verified
- [ ] Communication channels joined
- [ ] Credentials rotated if needed

**Authority Transfer**:
- [ ] Team notified of change
- [ ] Stakeholders informed
- [ ] Decision authority documented
- [ ] Escalation paths updated

**Context Transfer**:
- [ ] Current state documented
- [ ] In-progress work summarized
- [ ] Blockers and risks noted
- [ ] Historical decisions explained

### 5.3 Emergency Handoff

For unexpected handoffs:

1. **Immediate** (within hours):
   - Identify backup orchestrator
   - Grant minimal required access
   - Brief on critical work only

2. **Short-term** (within days):
   - Full access transfer
   - Documentation review
   - Stakeholder notification

3. **Stabilization** (within weeks):
   - Complete knowledge transfer
   - Process improvements from lessons learned
   - Update emergency procedures

---

## 6. Shared Knowledge Patterns

### 6.1 Organization-Wide Knowledge

Knowledge that should be shared across all teams:

| Knowledge Type | Location | Maintainer |
|----------------|----------|------------|
| **L2 Orchestration Profile** | Central repo | Primary Orchestrator |
| **Shared patterns library** | L2 profile | Primary + Contributors |
| **Model evaluation results** | Central docs | Primary Orchestrator |
| **Cross-team ADRs** | Central repo | Primary Orchestrator |
| **Training materials** | Wiki/LMS | Training lead |

### 6.2 Team-Specific Knowledge

Knowledge scoped to individual teams:

| Knowledge Type | Location | Maintainer |
|----------------|----------|------------|
| **L3 application docs** | App repo AOSD/ | Local Orchestrator |
| **Team-specific patterns** | L3 patterns/ | Local Orchestrator |
| **Context Nuggets** | AOSD/context/ | Local Orchestrator |
| **Session handoffs** | .ai/memory/handoffs/ | Current operator |

### 6.3 Knowledge Sharing Mechanisms

| Mechanism | Purpose | Cadence |
|-----------|---------|---------|
| **Pattern proposals** | Promote L3 patterns to L2 | As discovered |
| **Retrospectives** | Share learnings | End of each phase/sprint |
| **Office hours** | Q&A, guidance | Weekly |
| **Documentation reviews** | Ensure accuracy | Monthly |

---

## 7. Onboarding Checklist for New Orchestrators

### 7.1 Local Orchestrator Onboarding

**Week 1: Foundation**
- [ ] Read AOSD_FRAMEWORK.md (core principles)
- [ ] Read organization's L2 profile
- [ ] Meet with Primary Orchestrator
- [ ] Review team's L3 documentation
- [ ] Observe current orchestrator (shadow)

**Week 2: Practice**
- [ ] Complete SYLLABUS modules 1-3
- [ ] Create first slice specification
- [ ] Direct AI agent for simple task
- [ ] Review session with Primary Orchestrator
- [ ] Access all necessary tools

**Week 3: Independence**
- [ ] Manage full slice independently
- [ ] Handle first escalation/issue
- [ ] Participate in orchestrator sync
- [ ] Document lessons learned
- [ ] Receive feedback from Primary

**Week 4: Certification**
- [ ] Complete remaining SYLLABUS modules
- [ ] Demonstrate proficiency (assessed by Primary)
- [ ] Formally assigned Local Orchestrator role
- [ ] Added to orchestrator communication channels
- [ ] Begin regular participation

### 7.2 Primary Orchestrator Onboarding

In addition to Local Orchestrator requirements:

- [ ] Deep dive on L2 profile (create/modify capability)
- [ ] Model governance training
- [ ] Cross-team coordination experience
- [ ] Conflict resolution training
- [ ] Executive alignment meeting
- [ ] 90-day success criteria defined

---

## 8. Cross-References

This document connects to several other AOSD guides:

| Document | Relationship |
|----------|--------------|
| [AI Session Management](./DEVELOPMENT_WORKFLOW.md#6-ai-session-management) | Session handoffs for operators (#85) |
| [Context Nuggets](../AI_GUIDE.md#82-context-nuggets-and-jit-retrieval) | Shared team context (#86) |
| [Model Governance](./MODEL_GOVERNANCE.md) | Model version decisions (#89) |
| [Documentation Governance](./DOCUMENTATION_GOVERNANCE.md) | L1/L2/L3 consistency (#92) |

---

## 9. Summary

Scaling AOSD across organizations requires:

1. **Clear role hierarchy**: Primary → Local → Operator
2. **Defined authority boundaries**: Who decides what
3. **Regular synchronization**: Prevent drift across teams
4. **Phased adoption**: Awareness → Pilot → Expansion → Governance
5. **Proper handoffs**: Continuity when orchestrators change
6. **Shared knowledge**: Patterns, decisions, learnings flow across teams

The goal is consistent AOSD practice without bureaucratic overhead—enough structure to coordinate, enough flexibility to adapt.

---

**End of ORG_ADOPTION_AND_MULTI_ORCHESTRATOR.md**
