# AOSD Adoption Maturity Model

**Purpose**: This document defines the incremental adoption levels for AOSD, helping teams assess their current state and plan their adoption journey.

**Part of**: [AOSD Framework](./AOSD_FRAMEWORK.md)

*This section was extracted from AOSD_FRAMEWORK.md on 2024-12 to keep the framework AI-consumable.*

---

AOSD can be adopted incrementally. This maturity model helps teams assess their current state and plan their adoption journey.

## Level 0: Minimal

**Focus**: Get started with AOSD using the absolute minimum viable practices.

| Area | Characteristics |
|------|-----------------|
| **AI Agents** | Single agent, minimal constraints |
| **Documentation** | Single invariant: "don't break existing functionality" |
| **Planning** | Inline TodoWrite only (comments or mental model) |
| **Testing** | Fast tests only (unit tests that run in < 1 second) |
| **Version Control** | Basic commits, no formal branching strategy required |
| **Deployment** | Manual, human-controlled only |
| **Security** | Single trust rule: never touch production, never handle secrets |

**Use Cases for Level 0**:
- Prototypes and proofs of concept
- Experiments and spike work
- Learning AOSD methodology
- Personal projects
- Hackathons and time-boxed explorations
- Evaluating new AI tools or models

**Core Constraints (Non-Negotiable)**:
1. **Single Invariant**: Don't break existing functionality. If it works, don't make it not work.
2. **Single Trust Rule**: AI agents never touch production systems or handle secrets/credentials.
3. **Human Deploys**: All deployments are human-initiated and human-verified.

**What You Can Skip at Level 0**:
- Formal slice specifications (verbal or mental model is fine)
- Full TodoWrite workflow (inline comments suffice)
- Medium and slow test tiers
- Multi-environment setups
- ADRs and formal documentation
- Cost tracking
- Multi-agent workflows

**When to Upgrade from Level 0 to Level 1**:
Upgrade to Level 1 when any of these conditions apply:
- The project will be maintained beyond initial development
- Other people will work on or use the code
- The project will handle real user data
- The project will be deployed to production
- You've broken something that took more than 30 minutes to fix
- You find yourself wishing you had documented something

**Level 0 → Level 1 Transition Checklist**:
- [ ] Document at least 3-5 architectural invariants
- [ ] Write a slice spec for your next feature
- [ ] Set up formal version control branching
- [ ] Establish at least Dev and Prod environments
- [ ] Add human review before any production changes

---

## Level 1: Foundational

**Focus**: Establish basic AI-assisted development practices with human oversight.

| Area | Characteristics |
|------|-----------------|
| **AI Agents** | Single agent workflow (one AI assistant) |
| **Documentation** | Basic architectural invariants documented |
| **Planning** | Slice specifications for features |
| **Testing** | Basic testing (at least fast/unit tests) |
| **Version Control** | Simple branching strategy (feature branches) |
| **Deployment** | Manual deployments with human oversight |
| **Security** | Basic security review before deployment |

**You're Ready for Level 1 When**:
- You have access to an AI coding assistant
- You understand basic prompt engineering
- You have version control in place
- You can run tests locally

**Key Practices to Establish**:
- Document your architectural invariants (even a simple list)
- Write slice specs before implementing features
- Review all AI-generated code before committing
- Run tests before every commit

---

## Level 2: Structured

**Focus**: Implement systematic workflows and environment controls.

| Area | Characteristics |
|------|-----------------|
| **AI Agents** | Single agent with role-specific prompts |
| **Documentation** | ADRs, slice specs, prompt templates |
| **Planning** | TodoWrite workflow for complex tasks |
| **Testing** | Full testing tiers (Fast/Medium/Slow) |
| **Environments** | Environment isolation (Dev/Staging/Prod) |
| **Deployment** | CI/CD integration with automated tests |
| **Security** | Security scanning in CI/CD pipeline |
| **Observability** | Basic logging and error tracking |
| **Cost** | Cost visibility and basic tracking |

**You're Ready for Level 2 When**:
- Level 1 practices are consistently followed
- You have multiple environments available
- You have CI/CD infrastructure
- You're comfortable with AI-assisted development

**Key Practices to Establish**:
- Implement the Fast/Medium/Slow testing strategy
- Set up environment isolation
- Create prompt templates for common tasks
- Track AI usage costs
- Use TodoWrite for multi-step tasks

**Advancement Criteria** (Level 1 → Level 2):
- [ ] Invariants documented and followed consistently
- [ ] All features have slice specifications
- [ ] Test coverage > 70%
- [ ] No production deployments without human review
- [ ] At least 10 successful AI-assisted features delivered

---

## Level 3: Advanced

**Focus**: Multi-agent orchestration with automated trust and comprehensive observability.

| Area | Characteristics |
|------|-----------------|
| **AI Agents** | Multi-agent orchestration (Builder, Reviewer, Specialist) |
| **Documentation** | Living documentation, automated updates |
| **Planning** | Parallel development across agents/sessions |
| **Testing** | Property-based testing, fuzzing, mutation testing |
| **Environments** | Ephemeral environments, infrastructure as code |
| **Deployment** | Automated deployments with rollback |
| **Security** | Automated security validation, threat modeling |
| **Observability** | Comprehensive observability, AI-specific metrics |
| **Cost** | Cost optimization, predictive budgeting |
| **Recovery** | Automated failure detection and recovery |

**You're Ready for Level 3 When**:
- Level 2 practices are consistently followed
- You have experience with multiple AI models
- You understand failure modes and recovery patterns
- You have mature observability infrastructure

**Key Practices to Establish**:
- Define and implement agent roles (Builder, Reviewer, Specialist)
- Implement trust zones with appropriate oversight levels
- Set up AI-specific observability (model metrics, generation tracing)
- Automate failure detection and recovery
- Optimize costs based on usage patterns

**Advancement Criteria** (Level 2 → Level 3):
- [ ] CI/CD pipeline with all test tiers
- [ ] Environment isolation fully implemented
- [ ] TodoWrite used consistently for complex tasks
- [ ] Cost tracking in place with optimization actions
- [ ] At least 50 successful AI-assisted features delivered
- [ ] Failure modes documented and handled

---

## Maturity Assessment

Use this quick assessment to determine your current level:

| Question | Level 0 | Level 1 | Level 2 | Level 3 |
|----------|---------|---------|---------|---------|
| How many AI agents do you use? | 1 (minimal rules) | 1 | 1 with role prompts | Multiple specialized |
| Do you have slice specs? | None/informal | Some | All features | Auto-generated |
| Testing coverage? | Fast tests only | Unit tests | All tiers | + Property/Fuzz |
| Environment isolation? | None | Dev only | Dev/Staging/Prod | + Ephemeral |
| Deployment process? | Manual (human only) | Manual | CI/CD | Automated + Rollback |
| Cost tracking? | None | None | Basic visibility | Optimization |
| Failure handling? | None | Manual | Documented | Automated |
| Documented invariants? | 1 ("don't break") | 3-5 | Comprehensive | Living docs |

**Scoring**:
- Mostly "Level 0" answers → You're at Level 0 (suitable for prototypes, experiments, learning)
- Mostly "Level 1" answers → You're at Level 1
- Mix of "Level 1" and "Level 2" → Transitioning to Level 2
- Mostly "Level 2" answers → You're at Level 2
- Mix of "Level 2" and "Level 3" → Transitioning to Level 3
- Mostly "Level 3" answers → You're at Level 3

---

## Progression Guidance

### Don't Rush Advancement
- Each level builds on the previous
- Skipping levels creates gaps that cause problems later
- Master current level before advancing

### Signs You Should Advance
- Current practices feel routine and stable
- You're hitting limitations of current approach
- Team is ready for more sophisticated workflows
- Infrastructure supports next level requirements

### Signs You're Not Ready
- Current practices are inconsistent
- Frequent failures or rollbacks
- Team is overwhelmed with current complexity
- Infrastructure gaps would create workarounds

### Recommended Timeline
- **Level 1**: 1-2 months to establish
- **Level 2**: 2-4 months after Level 1 mastery
- **Level 3**: 3-6 months after Level 2 mastery

*Note: Timelines vary based on team size, project complexity, and existing infrastructure.*
