# Orchestrator Handoff Template

**AOSD Framework - Orchestrator Responsibility Transfer**

---

> **Instructions**: Use this template when transferring orchestrator responsibilities. Complete all applicable sections. Delete this instruction block when done.

---

# Orchestrator Handoff

**Handoff Date**: [YYYY-MM-DD]
**Handoff Type**: [ ] Temporary (vacation) / [ ] Permanent (role change) / [ ] Emergency

---

## 1. Parties

### Outgoing Orchestrator
- **Name**: [Name]
- **Role**: [ ] Primary Orchestrator / [ ] Local Orchestrator
- **Scope**: [Team/Project/Organization]
- **Duration in Role**: [Time period]
- **Last Day**: [Date or "N/A" if temporary]

### Incoming Orchestrator
- **Name**: [Name]
- **Previous Role**: [If any AOSD experience]
- **Start Date**: [Date]
- **Onboarding Completed**: [ ] Yes / [ ] In progress / [ ] Not yet

### Backup Contact
- **Name**: [Name for escalations if incoming needs help]
- **Contact Method**: [Email/Slack/Phone]

---

## 2. Scope of Responsibility

### Projects/Teams Covered
| Project/Team | L3 Location | Status | Notes |
|--------------|-------------|--------|-------|
| [Project 1] | [repo/AOSD/] | Active | [any context] |
| [Project 2] | [repo/AOSD/] | Active | |

### L2 Profile Responsibilities (if Primary)
- **Profile Location**: [repo URL]
- **Last Updated**: [date]
- **Pending Changes**: [any in flight]

### AI Agents in Use
| Agent | Access Level | Primary Use |
|-------|--------------|-------------|
| [Claude Code] | [Full/Limited] | [Builder] |
| [ChatGPT] | [Full/Limited] | [Reviewer] |

---

## 3. Current State

### Open Work Items
| ID | Description | Status | Priority | Next Action |
|----|-------------|--------|----------|-------------|
| [#123] | [description] | [status] | [H/M/L] | [what's needed] |
| [#124] | | | | |

### In-Progress Slices
| Slice ID | Description | % Complete | Blocked By |
|----------|-------------|------------|------------|
| [SLICE-042] | [description] | [70%] | [nothing / blocker] |
| | | | |

### Pending Decisions
| Decision | Context | Options | Recommended | Due |
|----------|---------|---------|-------------|-----|
| [Decision 1] | [why needed] | [A, B, C] | [recommendation] | [date] |
| | | | | |

### Known Issues/Risks
| Issue | Impact | Mitigation | Owner |
|-------|--------|------------|-------|
| [Issue 1] | [impact description] | [current mitigation] | [who handles] |
| | | | |

---

## 4. Key Relationships

### Stakeholders
| Name | Role | Relationship | Communication Cadence |
|------|------|--------------|----------------------|
| [Name] | [Product Owner] | [Works closely on priorities] | [Weekly 1:1] |
| | | | |

### Other Orchestrators
| Name | Scope | Coordination Points |
|------|-------|---------------------|
| [Name] | [Team B] | [Shared API, need to sync on changes] |
| | | |

### External Dependencies
| Dependency | Contact | Notes |
|------------|---------|-------|
| [Cloud team] | [name@company.com] | [For infrastructure changes] |
| | | |

---

## 5. Access Transfer

### Systems Access
| System | Current Status | Action Needed |
|--------|----------------|---------------|
| GitHub/GitLab | [ ] Granted | [ ] None / [ ] Grant to incoming |
| AI Agent (Claude Code) | [ ] Granted | [ ] None / [ ] Grant to incoming |
| AI Agent (ChatGPT/other) | [ ] Granted | [ ] None / [ ] Grant to incoming |
| Cloud Console | [ ] Granted | [ ] None / [ ] Grant to incoming |
| CI/CD System | [ ] Granted | [ ] None / [ ] Grant to incoming |
| Communication (Slack/Teams) | [ ] Granted | [ ] None / [ ] Add to channels |

### Credentials to Rotate
| Credential | Location | Rotation Needed |
|------------|----------|-----------------|
| [API key X] | [Secrets Manager] | [ ] Yes / [ ] No |
| | | |

---

## 6. Knowledge Transfer Sessions

### Scheduled Sessions
| Date | Topic | Duration | Materials |
|------|-------|----------|-----------|
| [date] | L2/L3 overview | [1 hr] | [links] |
| [date] | Current work walkthrough | [1 hr] | [links] |
| [date] | Stakeholder introductions | [30 min] | N/A |
| [date] | Q&A and final handoff | [1 hr] | N/A |

### Key Documents to Review
- [ ] L2 Orchestration Profile
- [ ] L3 AOSD.md for each project
- [ ] Recent ADRs (last 3 months)
- [ ] Current slice specifications
- [ ] Session handoffs in .ai/memory/handoffs/

### Recorded Knowledge (if available)
| Recording | Topic | Location |
|-----------|-------|----------|
| [link] | [topic] | [where stored] |

---

## 7. First Week Priorities

For the incoming orchestrator:

### Day 1
- [ ] Verify all system access works
- [ ] Read L2 profile end-to-end
- [ ] Review current slice specifications
- [ ] Meet with outgoing orchestrator (if available)

### Day 2-3
- [ ] Walk through each active project's L3 docs
- [ ] Review open work items and their context
- [ ] Attend scheduled team meetings
- [ ] Ask questions from initial review

### Day 4-5
- [ ] Take over at least one active slice
- [ ] Direct AI agent for a small task
- [ ] Connect with key stakeholders
- [ ] Document any gaps in handoff

### Week 1 Exit Criteria
- [ ] Can locate all critical documentation
- [ ] Understands current project state
- [ ] Has met key stakeholders
- [ ] Completed at least one slice/task
- [ ] Knows escalation paths

---

## 8. Post-Handoff

### Outgoing Orchestrator Availability
- **Available for questions until**: [date]
- **Best contact method**: [email/Slack/phone]
- **Response time expectation**: [same day / within 24 hours]

### Checkpoints
| Date | Check | Owner |
|------|-------|-------|
| [+1 week] | How's it going? Any blockers? | Primary Orchestrator |
| [+2 weeks] | Full independence check | Primary Orchestrator |
| [+1 month] | Handoff retrospective | Both parties |

### Success Metrics
The handoff is successful when:
- [ ] Incoming orchestrator can manage day-to-day independently
- [ ] No critical decisions delayed due to handoff
- [ ] Team reports smooth transition
- [ ] No escalations due to knowledge gaps

---

## 9. Emergency Contacts

If incoming orchestrator is blocked:

| Issue Type | Contact | Method |
|------------|---------|--------|
| Technical blocker | [name] | [contact] |
| Process question | [Primary Orchestrator] | [contact] |
| Access issue | [IT/Admin] | [contact] |
| Urgent business decision | [stakeholder] | [contact] |

---

## 10. Sign-off

### Outgoing Orchestrator
- **I confirm**: All critical information has been documented and transferred.
- **Signature**: _________________ **Date**: _________

### Incoming Orchestrator
- **I confirm**: I have received the handoff materials and understand my responsibilities.
- **Signature**: _________________ **Date**: _________

### Primary Orchestrator (if applicable)
- **I approve**: This handoff meets organizational requirements.
- **Signature**: _________________ **Date**: _________

---

## 11. Notes

[Any additional context, concerns, or recommendations]

---

**End of Orchestrator Handoff**
