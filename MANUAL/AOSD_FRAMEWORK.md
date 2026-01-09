# AOSD Framework
**AI Orchestrated Systems Development (AOSD) - Core Methodology**
**Environment-Agnostic Principles and Patterns**
**Author: Jim Wiedman, co-created with AI collaborators ChatGPT and Claude**
**Copyright Algorail AI, LLC 2025**
**Version: 1.0**

---

# 📘 Purpose of This Framework

This document defines the **core AOSD methodology**—the environment-agnostic principles, patterns, and practices for orchestrating AI agents to build and maintain software systems.

Unlike implementation-specific documentation, this framework:
- **Does not prescribe** specific cloud platforms, AI models, or tools
- **Does define** the principles that make AI orchestration safe, predictable, and effective
- **Can be adapted** to any cloud environment, AI toolset, or organizational context
- **Provides structure** for orchestrators to make their own implementation choices

---

## Note for AI Agents

If you are an AI agent consuming this repository to assist a human with AOSD, **do not attempt to load this entire manual by default**.

Instead, first read:

- **`AI_GUIDE.md`** (at the repository root)

That guide explains:
- How to read the AOSD docs in a token-efficient way
- Which sections to load for which kinds of tasks
- How to extract and apply AOSD principles and patterns
- How to guide humans through AOSD end-to-end

Use `AI_GUIDE.md` as your entrypoint before loading specific sections of this manual.

---

## Historical Context and Influences

For non-normative information about the influences, prior art, and acknowledgments that informed AOSD's design, see [`AOSD_INFLUENCES_AND_ACKNOWLEDGMENTS.md`](../AOSD_INFLUENCES_AND_ACKNOWLEDGMENTS.md).

---

## Framework Map (for Humans and AI)

This map helps navigate the framework efficiently. AI agents should use anchor comments (e.g., `<!-- AOSD:SECTION:CORE_PRINCIPLES -->`) to locate sections.

| Section | Purpose | When to Read |
|---------|---------|--------------|
| Why AOSD Exists | Goals and motivation | First read |
| Core Principles | Non-negotiable rules (12 principles) | Before any design |
| Maturity Model | Adoption levels (0-3) | Assessing readiness |
| Architecture Patterns | Reference patterns (9 patterns) | During implementation |
| Integrating AI Into Applications | Runtime AI capabilities | Adding AI features |
| Development Workflow | Work process guidance | Setting up workflows |
| AI Agent Roles | Agent responsibilities | Multi-agent setup |
| Security & Compliance | Security principles | Security-critical work |
| Restrictive Environments | Constrained contexts | Network-isolated/restricted |
| Failure Modes & Recovery | Error handling | Debugging, recovery |
| Observability | Monitoring AI systems | Production setup |
| Adoption Guide | Getting started | New adopters |
| Scaling for Teams | Team coordination | Growing teams |

**Available Anchors** (for AI agents):
- `<!-- AOSD:SECTION:CORE_PRINCIPLES -->`
- `<!-- AOSD:SECTION:STARTER_INVARIANTS -->`
- `<!-- AOSD:SECTION:RUNTIME_INVARIANTS -->`
- `<!-- AOSD:SECTION:EVIDENCE_PACKAGES -->`
- `<!-- AOSD:SECTION:MATURITY_MODEL -->`
- `<!-- AOSD:SECTION:ARCHITECTURE_PATTERNS -->`

---

# ⭐ Why AOSD Exists

Modern AI systems can generate code, documentation, tests, and architecture at a level that was unimaginable only a few years ago. These capabilities dramatically expand what a single developer or small team can build. But AI alone—no matter how powerful—does not automatically produce secure, maintainable, or production-ready systems.

Without structure, AI-generated software tends to drift, lose coherence, accumulate hidden risks, and become difficult for a new human to understand or modify. Effective prompting is not enough. What's missing is a **methodology** that channels AI's capabilities into *reliable, sustainable, long-lived systems*.

**AOSD exists to provide that methodology.**
It defines the principles, patterns, and constraints needed for AI agents and human orchestrators to collaborate safely and effectively on real applications.

AOSD is built to achieve six high-level goals that ensure AI-generated applications remain robust, secure, and sustainable over time:

---

## Goal 1 — Build Sustainable Systems That Outlive Their Original Developers

AI accelerates development, but without structure, the resulting system often becomes tied to the person who built it.
AOSD prevents **human-dependency debt** by enforcing predictable architecture, consistent patterns, AI-maintained documentation, and full traceability, so new orchestrators can take over quickly and safely.

---

## Goal 2 — Engineer Security and Trustworthiness from the Start

Security must be designed in—not added later.
AOSD applies principles inspired by NIST SP 800-160 to make security a continuous engineering practice: invariants, isolation, guardrails, decision logs, and ongoing AI-assisted checks throughout development and runtime.

---

## Goal 3 — Accelerate Development and Keep Evolution Fast, Safe, and Low-Cost

AI can generate code quickly, but sustainable velocity requires structure.
AOSD uses slices, invariants, trust zones, and clear workflows to ensure both initial development and future changes remain fast, predictable, and low-risk.

---

## Goal 4 — Minimize Operational Cost Through Lean, Cloud-Native Architecture

AI can generate complex systems, but complexity increases cost.
AOSD emphasizes serverless-first patterns, pay-per-use execution, minimal dependencies, and AI cost awareness so applications remain affordable to run and scale.

*Serverless-first does not mean serverless-only. AOSD encourages serverless by default but supports containerized or specialized workloads where they are the appropriate architectural choice.*

---

## Goal 5 — Integrate AI Into Applications as First-Class Runtime Capabilities

AI shouldn't only help build the application; it should also enhance it.
AOSD supports adding intelligent features such as validation, synthesis, reasoning, contextual assistance, and domain-specific workflows directly into the application itself.

---

## Goal 6 — Embed Training and Knowledge Directly Into Applications

AI enables applications to train their users directly.
AOSD promotes inline explanations, integrated help, up-to-date documentation, and AI-assisted onboarding so users can understand and adopt systems without external training materials.

---

# ⭐ Why Prompts Alone Are Not Enough

Prompting can produce *pieces* of a system, but building and maintaining a reliable application requires:

- Stable architecture and consistent patterns
- Cross-agent coordination and controlled autonomy
- Traceability and validated changes
- Security and guardrails for correctness
- Predictable cost and maintainability over time
- Integrated documentation and human-AI handoffs

No prompt—no matter how well engineered—can enforce these constraints on its own.

AOSD provides the **structure**, **principles**, and **operational discipline** that prompting alone cannot.
It is the framework that ensures AI-generated systems are not just fast to build, but **robust, secure, maintainable, evolvable, and trustworthy over time**.

---

<!-- AOSD:SECTION:MATURITY_MODEL -->
# 📈 Adoption Maturity Model

AOSD can be adopted incrementally across four levels (0-3), from minimal practices suitable for prototypes to advanced multi-agent orchestration.

**Quick Reference**:

| Level | Focus | AI Agents | Key Characteristics |
|-------|-------|-----------|---------------------|
| 0 | Minimal | Single, minimal rules | Prototypes, experiments, learning |
| 1 | Foundational | Single agent | Basic invariants, slice specs, human oversight |
| 2 | Structured | Role-specific prompts | Full test tiers, CI/CD, environment isolation |
| 3 | Advanced | Multi-agent orchestration | Automated validation, ephemeral environments |

See [MATURITY_MODEL.md](./MATURITY_MODEL.md) for full content including:
- Detailed characteristics for each level
- Advancement criteria and checklists
- Maturity assessment questions
- Progression guidance and timelines

*This section was extracted on 2024-12 to keep the framework AI-consumable.*

---

<!-- AOSD:SECTION:CORE_PRINCIPLES -->
# 🎯 Core Principles

The following twelve principles translate AOSD's [high-level goals](#-why-aosd-exists) into actionable practices. Each principle directly supports one or more goals, ensuring that day-to-day development decisions align with AOSD's overarching vision of building robust, secure, maintainable, and human-centered systems.

---

## 1. Multi-Agent Orchestration

**Supports Goals**: 1 (Sustainability), 3 (Fast Evolution), 5 (AI Runtime)

**Principle**: Software development is orchestrated across multiple AI agents, each with specialized roles and responsibilities, all operating under the direction of a Human Orchestrator.

**Key Concepts**:
- **Human Orchestrator**: The human who directs all AI agent activities, sets strategic direction, defines invariants, determines trust zones, and maintains final authority over all decisions. This is the central coordinating role in AOSD.
- **Role Separation**: Different agents handle different aspects (architecture, implementation, review, testing)
- **Agent Handoffs**: Clear protocols for passing work between agents
- **Context Management**: Consistent context provided to all agents via prompts and documentation

**Human Orchestrator Responsibilities**:
- Sets strategic direction and defines slice boundaries
- Selects and assigns AI agents to appropriate roles
- Defines and enforces architectural invariants
- Determines trust zones and autonomy levels (see Principle 8)
- Approves critical decisions and resolves conflicts
- Reviews outputs at appropriate checkpoints
- Maintains final authority over all workflow outcomes

**Implementation Requirements**:
- Designate a Human Orchestrator for each piece of work
- Define roles for each AI agent in your environment
- Establish prompt contracts that specify agent responsibilities
- Create handoff protocols between agents
- Maintain documentation that provides stable context
- Document trust boundaries for each agent role

---

## 2. Trustworthy-by-Design Architecture

**Supports Goals**: 1 (Sustainability), 2 (Security)

**Principle**: Systems must be designed with security, reliability, and auditability as foundational requirements, not afterthoughts.

**Key Concepts**:
- **Security First**: Security controls are architectural, not procedural
- **Audit-Friendly**: All operations are traceable and reversible
- **Fail-Safe Defaults**: Systems default to secure states
- **Defense in Depth**: Multiple layers of protection

**Behavior Under Stress**:
- **Predictable Degradation**: When resources become constrained, systems shed non-critical functions gracefully rather than failing catastrophically
- **Critical Function Preservation**: Core safety and data-integrity functions continue operating even when secondary features are unavailable
- **Fail-Safe States**: Upon unrecoverable errors, systems transition to known-safe states that prevent data corruption or security breaches
- **Recovery-First Design**: Systems include explicit recovery paths—automatic retry, manual intervention hooks, or graceful restart—rather than assuming success
- **Bounded Blast Radius**: Failures are contained to the smallest possible scope through isolation, bulkheads, and circuit breakers

**Implementation Requirements**:
- Implement authentication and authorization at architectural boundaries
- Design for traceability and observability
- Use immutable infrastructure patterns where possible
- Separate concerns between security layers

---

## 3. Environment Isolation

**Supports Goals**: 2 (Security), 3 (Fast Evolution)

**Principle**: Clear boundaries between development, staging, and production environments prevent data leakage and ensure safe operations.

**Key Concepts**:
- **Development Environment**: Source of truth for code, accessed via secure methods
- **Production Environment**: Controlled deployment target with strict access controls
- **Local/Client Environment**: Thin client for remote access and test automation only
- **Data Isolation**: Production data never flows into development
- **Access Controls**: Environment access requires authentication and authorization

**Implementation Requirements**:
- Define your environment boundaries clearly
- Implement secure access methods (no direct SSH, require MFA, etc.)
- Establish data flow rules between environments
- Document promotion workflows (Dev → Staging → Prod)

---

## 4. Architectural Invariants

**Supports Goals**: 1 (Sustainability), 2 (Security)

**Principle**: Non-negotiable architectural rules ensure consistency and prevent AI agents from introducing instability.

**Key Concepts**:
- **Invariants**: Rules that must never be violated
- **Pattern Library**: Reusable solutions to common problems
- **AI Guardrails**: Explicit instructions for what AI agents must/must not do
- **Fitness Functions**: Automated checks that validate invariants

**Implementation Requirements**:
- Document your architectural invariants explicitly
- Provide AI agents with clear guardrails
- Implement automated validation where possible
- Create a pattern library for common solutions

### Types of Invariants

Invariants fall into two categories based on when they can be validated:

**Build-Time Invariants**: Properties validated during build, test, or deployment. These are enforced through CI/CD pipelines, linters, and deployment gates.

Examples:
- "All Lambda functions use the approved bundling pattern"
- "No direct DynamoDB access outside the data layer"
- "All API endpoints require authentication middleware"
- "Infrastructure changes require Terraform plan review"

**Runtime Invariants**: Properties that can only be validated during system operation. These describe *what must remain true* while the system runs.

Examples:
- "PII never appears in application logs"
- "Authentication tokens are never logged or exposed"
- "No single tenant exceeds X% of shared resources"
- "All API responses complete within Y ms at p99"
- "Database connections never exceed pool limits"

<!-- AOSD:SECTION:RUNTIME_INVARIANTS -->
### Runtime Invariants

Runtime invariants extend the architectural invariants concept to cover properties that require continuous validation during operation—not just at build or deploy time.

**Key Distinction**: Runtime invariants describe *what must remain true*, not *how monitoring is implemented*. The invariant is the property; enforcement mechanisms are Level 2/3 concerns.

**Categories of Runtime Invariants**:

| Category | Example Invariants |
|----------|-------------------|
| **Data Protection** | "PII never in logs", "Secrets never in responses" |
| **Performance** | "p99 latency < threshold", "Error rate < X%" |
| **Resource Isolation** | "Tenant resource caps enforced", "No noisy neighbors" |
| **Security** | "Auth tokens never logged", "Rate limits enforced" |
| **Availability** | "Circuit breakers prevent cascade failures" |

**Conceptual Enforcement Patterns**:

Runtime invariants require enforcement mechanisms. At Level 1, we describe patterns conceptually—specific tooling choices belong at Level 2/3:

- **Log Scrubbers / PII Detectors**: Validate data protection invariants by scanning output streams
- **SLO Monitors and Alerts**: Track performance invariants against defined thresholds
- **Runtime Policy Enforcement**: Apply resource limits and access controls during execution
- **Circuit Breakers and Resource Guards**: Prevent resource exhaustion and cascade failures

**Connection to Observability**: Runtime invariants require observability infrastructure to detect violations. You cannot enforce what you cannot observe. However, observability tooling (specific APM solutions, log aggregators, metrics platforms) is a Level 2 implementation choice.

**Important Clarification**: This is a *conceptual framework* at Level 1. AOSD does not prescribe specific monitoring tools, observability platforms, or enforcement infrastructure. Those decisions belong in your Level 2 Orchestration Profile based on your platform, compliance requirements, and operational maturity.

<!-- AOSD:SECTION:STARTER_INVARIANTS -->
### Starter Invariants

New adopters often face blank-page paralysis when defining invariants. This section provides a **canonical minimal set** of universal invariants to accelerate adoption. These are **safe defaults, not universal truths** — adapt or extend them based on your context.

**Purpose**: Day-1 defaults that work for any environment. Start here, then add domain-specific invariants as your system evolves.

#### Security Invariants

| ID | Invariant | Rationale |
|----|-----------|-----------|
| **S1** | **No credentials in code** — Secrets, API keys, and credentials are never committed to version control | Secrets in code are the #1 preventable security incident. Use environment variables, secret managers, or vault services. |
| **S2** | **Least privilege by default** — Access permissions are minimally scoped; broad access requires explicit justification | Reduces blast radius of compromises. Document exceptions via ADR when broader access is required. |

#### Ownership Invariants

| ID | Invariant | Rationale |
|----|-----------|-----------|
| **O1** | **Ownership declared** — Every data store, API, and service has a declared owner/source-of-truth | Prevents orphaned resources and unclear accountability. If no one owns it, no one maintains it. |
| **O2** | **Boundaries respected** — Components do not directly modify data owned by other components | Maintains separation of concerns and prevents hidden coupling. Cross-boundary access goes through defined interfaces. |

#### Traceability Invariants

| ID | Invariant | Rationale |
|----|-----------|-----------|
| **T1** | **State changes are traceable** — All mutations to persistent state can be traced to a request, user, or system event | Essential for debugging, auditing, and incident response. You cannot fix what you cannot trace. |
| **T2** | **Decisions are logged** — Security-relevant and business-critical decisions are recorded with context | Provides audit trail for compliance and incident analysis. Context includes who, what, when, and why. |

#### Safety Invariants

| ID | Invariant | Rationale |
|----|-----------|-----------|
| **SF1** | **Rollback possible** — Changes to production have a documented rollback procedure | No change should be a one-way door. Even if rollback is "redeploy previous version," document it. |
| **SF2** | **Fail-safe defaults** — Systems fail closed (secure) rather than open (permissive) | When in doubt, deny access or stop processing. Permissive failures compound into security incidents. |

#### How to Use Starter Invariants

1. **Start with these** — Adopt all 8 as your baseline unless you have explicit reasons not to
2. **Add domain-specific invariants** — Your application will have invariants specific to your domain (e.g., "PII never in logs" for healthcare, "All trades are idempotent" for fintech)
3. **Modify with ADRs** — If your context requires modifying a starter invariant, document the reasoning in an Architecture Decision Record. "We relaxed SF2 because..." is a valid decision when justified
4. **Validate continuously** — Connect invariants to automated checks where possible (build-time) or monitoring (runtime). See "Evidence Packages" in Principle 6

#### What Starter Invariants Are NOT

- **Exhaustive**: These are starting points, not a complete security or architecture review
- **Mandatory**: AOSD is not prescriptive — if an invariant doesn't fit, document why and move on
- **Sufficient**: Domain-specific risks require domain-specific invariants beyond this list
- **Static**: Revisit and evolve invariants as your system matures

**Connection to Evidence Packages**: When producing Evidence Packages (see Principle 6), include which starter invariants were validated and how. This creates traceable proof that baseline safety checks were performed.

---

## 5. Slice-Based Development

**Supports Goals**: 3 (Fast Evolution), 4 (Low Cost)

**Principle**: Work is broken into vertical slices that deliver end-to-end value incrementally.

**Key Concepts**:
- **Vertical Slices**: Each slice spans from UI to data layer
- **Incremental Delivery**: Slices can be deployed independently
- **Risk Management**: Smaller changes reduce blast radius
- **Fast Feedback**: Each slice can be tested and validated quickly

**Implementation Requirements**:
- Create slice specifications before implementation
- Design slices to be independently deployable
- Use feature flags for gradual rollout when needed
- Test each slice end-to-end

---

## 6. TodoWrite Workflow

**Supports Goals**: 1 (Sustainability), 2 (Security), 3 (Fast Evolution)

**Principle**: Complex tasks must be planned and tracked explicitly to prevent AI agents from losing context or skipping steps. Risk-based tiers determine when TodoWrite is required and what level of human oversight is needed.

**Key Concepts**:
- **Planning Before Action**: Break down complex tasks before executing
- **Visibility**: User can see progress and remaining work
- **Accountability**: Each task is marked complete only when fully done
- **Adaptation**: Plans can be adjusted based on learnings
- **Risk-Based Planning**: Effort invested in planning should match the risk level

### Risk-Based TodoWrite Tiers

Not all changes carry equal risk. AOSD uses tiered risk levels to calibrate planning effort and human oversight:

| Tier | Risk Level | Examples | HITL Requirement | TodoWrite |
|------|------------|----------|------------------|-----------|
| **High** | Security, data, production | IAM policies, DB migrations, auth changes, prod config | Full review required | Mandatory |
| **Medium** | Business logic, integrations | API endpoints, service calls, handlers | Spot check | Recommended |
| **Low** | UI, tests, documentation | Component styling, unit tests, comments | Automated only | Optional |
| **Zero** | Generated, trivial | Boilerplate, imports, formatting, typos | None | Skip |

**Risk Assessment Criteria**:
- Touches invariants or security boundaries → **High**
- Multi-file or architectural change → **High**
- Code logic or business rules → **Medium**
- Documentation or comments only → **Low**
- Formatting or trivial fixes → **Zero**

**Why Tiers Matter**:
- Prevents over-engineering trivial changes
- Ensures critical changes get proper oversight
- Gives AI and humans a shared mental model for risk
- Supports security-by-design without slowing low-risk work

**Implementation Requirements**:
- AI agents must assess risk tier before proceeding
- High-risk changes require full TodoWrite with human approval
- Medium-risk changes should use TodoWrite unless explicitly waived
- Low-risk changes may skip TodoWrite but maintain awareness
- Zero-risk changes proceed without TodoWrite
- Tasks must have clear completion criteria
- One task should be in-progress at a time
- Completed tasks marked immediately upon completion

<!-- AOSD:SECTION:EVIDENCE_PACKAGES -->
### Evidence Packages

An **Evidence Package** is a structured artifact accompanying changes that proves safety and intent. It is the "unit of trust" — a bundle that explains *why a change is safe*, not just that it passed CI.

**Why Evidence Packages Matter**:
- **Auditability**: Provides traceable proof of due diligence
- **Accountability**: Links changes to declared risks and assessments
- **Compliance Readiness**: Bridges good engineering hygiene to regulatory-grade requirements
- **Incident Response**: Immutable record of what was true at the time of change

**Key Property — Proves Due Diligence, Not Correctness**: An evidence package does not prove a change is correct — it proves that declared risks were assessed and appropriate checks were performed under the stated risk tier.

**Key Property — Immutable Once Produced**: The evidence package represents what was true *at the time of change*. Once produced, it should not be modified. This matters for audit trails and incident response.

**Required Contents**:
- Slice ID (reference to the work item)
- Risk tier (High/Medium/Low/Zero)
- Tests executed + proof (CI links, artifacts, or logs)
- Security scans + proof (scan results or attestations)
- Invariants validated (confirmation that architectural invariants were checked)
- Rollback procedure reference (link to or description of how to revert)

**Optional Contents**:
- Approver (who reviewed/approved the change)
- Deployment target (environment deployed to)
- Related ADRs (architectural decisions relevant to this change)

**What is NOT Included by Default**:
- **Raw AI prompts**: Not included due to liability concerns (potential PII, secrets, legal discovery implications). If prompt logging is needed for compliance, handle it separately with appropriate data governance.

**Storage Guidance**:
- Default recommendation: Store as CI artifact attached to the build/deployment
- Regulated environments may require longer retention or external stores
- Storage mechanism is a **Level 2 decision** — define in your Orchestration Profile

**Implementation Requirements**:
- High-risk changes require evidence packages
- Medium-risk changes should produce evidence packages
- Low-risk changes may produce lightweight evidence (or skip per team policy)
- Zero-risk changes do not require evidence packages
- Evidence package template and storage location defined at Level 2

### AI Provenance

**AI Provenance** is metadata about AI-assisted changes that enables traceability, accountability, and debugging. As AI agents perform increasing amounts of development work, understanding *what the AI did* becomes critical.

**Why AI Provenance Matters**:
- **Accountability**: Links changes to specific agents, models, and contexts
- **Debugging**: Helps diagnose why an AI made particular decisions
- **Audit**: Enables tracing changes back to specific models, versions, and configurations
- **Drift Detection**: Reveals whether AI output quality is changing over time
- **Compliance**: Supports regulatory requirements for AI-assisted systems

**Default Provenance (Metadata-Only)**:

By default, AOSD recommends capturing lightweight metadata that identifies the AI involvement without creating storage, privacy, or legal burdens:

| Field | Description | Example |
|-------|-------------|---------|
| **Model Name & Version** | The AI model that performed the work | `claude-3-5-sonnet-20241022` |
| **Agent Role** | The AOSD role the agent was fulfilling | `Builder`, `Reviewer`, `Architect` |
| **Timestamp** | When the AI interaction occurred | `2024-10-15T14:30:00Z` |
| **Slice ID / Intent Reference** | The work item or slice being worked on | `#123`, `slice-auth-refactor` |
| **Toolchain Version** | Version of the orchestration tooling | `claude-code v1.2.3` |

**What is NOT Captured by Default**:

The following are explicitly *not* included in default AI provenance:

| Excluded | Rationale |
|----------|-----------|
| **Raw prompts** | May contain PII, secrets, proprietary information; creates legal discovery liability |
| **Full context windows** | Excessive storage; often contains sensitive data from codebase |
| **Intermediate reasoning** | Model-internal; not reliably available; storage burden |
| **Complete conversation logs** | Privacy concerns; storage costs; rarely needed for audit |

**Rationale for Exclusions**:
- Prompts frequently include code snippets, configuration, or user context that may be sensitive
- Legal discovery in litigation could compel disclosure of logged prompts
- Storage and retention complexity for large context windows is substantial
- Most audit and debugging needs are satisfied by metadata alone

**Opt-In High-Audit Mode**:

Environments with strict compliance requirements (financial services, healthcare, government) may need richer AI tracing. This is a **Level 2 concern** — define in your Orchestration Profile:

- Enable prompt logging with appropriate redaction (PII, secrets, credentials)
- Implement access controls on logged data
- Define retention limits aligned with regulatory requirements
- Consider separate storage with restricted access
- Document data governance policies for AI interaction logs

**Connection to Evidence Package**:

When AI assists with a change, AI provenance metadata should be included in the Evidence Package:

- Add an `AI Assistance` section to the Evidence Package
- Include the default provenance fields (model, role, timestamp, slice ID)
- Note whether high-audit logging was enabled for this change
- Reference any AI-specific review checkpoints that were applied

**Implementation Requirements**:
- Capture default provenance metadata for all AI-assisted changes
- Store provenance as part of commit metadata, PR description, or Evidence Package
- Do not log raw prompts by default — treat prompt content as sensitive
- High-audit mode is opt-in and requires explicit data governance policies
- Provenance storage mechanism is a **Level 2 decision**

---

## 7. Testing Strategy (Fast/Medium/Slow)

**Supports Goals**: 2 (Security), 3 (Fast Evolution), 4 (Low Cost)

**Principle**: Tests are organized by speed and scope to enable fast feedback while maintaining comprehensive coverage.

**Key Concepts**:
- **Fast Tests**: Unit tests, pure functions, no external dependencies (< 1s)
- **Medium Tests**: Integration tests with mocked external services (< 10s)
- **Slow Tests**: End-to-end tests with real services (< 5min)
- **Test Pyramid**: More fast tests, fewer slow tests

**Implementation Requirements**:
- Categorize tests by speed tier
- Run fast tests frequently during development
- Run slow tests before deployment
- Design for testability (pure functions, dependency injection)

### Advanced Testing Techniques

**Property-Based Testing**: For critical logic, consider property-based testing (e.g., Hypothesis for Python, fast-check for TypeScript) to systematically explore edge cases. AI-generated code tends to handle happy paths well but often misses boundary conditions, null cases, and unusual input combinations. Property tests discover these gaps by generating hundreds of randomized test cases based on invariants you define.

**Mutation Testing**: For high-risk modules, mutation testing validates that tests actually catch behavioral changes, not just achieve coverage numbers. AI can generate tests that look comprehensive but don't assert meaningful behavior—they pass regardless of what the code does. Mutation testing (e.g., mutmut for Python, Stryker for JavaScript) exposes these weak tests by making small changes to code and verifying tests fail.

### AI-Specific Test Considerations

When AI generates code, additional test focus areas emerge:

| Concern | What to Test | Why |
|---------|--------------|-----|
| **Hallucinated Imports** | Import statements resolve | AI may invent packages that don't exist |
| **API Correctness** | Function signatures match real APIs | AI may use non-existent methods or wrong parameters |
| **Code Preservation** | Existing functionality still works | AI may remove code during refactoring |
| **Regression After Refactor** | Behavior unchanged after AI changes | AI refactoring can subtly alter logic |
| **Security Patterns** | Auth checks, input validation present | AI may skip security in favor of functionality |

**Recommended Checks**:
```bash
# Verify imports resolve (Python)
python -c "from your_module import *"

# Type check for API correctness (TypeScript)
npx tsc --noEmit

# Run existing tests after any AI change
pytest  # or npm test
```

**Test Review Checklist for AI-Generated Code**:
- [ ] Tests assert behavior, not just "no error"
- [ ] Edge cases covered (empty, null, max values)
- [ ] Imports verified to exist
- [ ] No existing tests removed or weakened
- [ ] Security-relevant code has explicit tests

### Test-Driven Development (TDD) with AI

**Recommended Practice**: Use TDD (Test-Driven Development) when working with AI agents, especially for complex logic and critical paths.

**Why TDD Aligns with AI-Assisted Development**:

| TDD Benefit | AI Alignment |
|-------------|--------------|
| **Tests as specification** | AI can generate implementation from well-written tests |
| **Clear acceptance criteria** | Tests define "done" unambiguously for AI |
| **Immediate feedback** | AI can verify implementation against tests in real-time |
| **Regression prevention** | Tests anchor behavior before AI refactoring |
| **Design emergence** | Writing tests first forces AI toward testable, modular code |

**AOSD TDD Workflow**:

1. **Write test from spec**: Human or AI translates slice spec into test cases
2. **Verify test fails**: Confirm test correctly captures expected behavior
3. **AI implements**: Builder agent writes code to make tests pass
4. **Refactor**: AI improves code structure while tests ensure behavior preservation
5. **Repeat**: Continue for each acceptance criterion

**TDD with AI Agents**:

```
Example prompt for TDD workflow:

"Write fast tests for this behavior first:
- Given a user with email, return normalized lowercase email
- Given null email, raise ValidationError
- Given email with whitespace, trim and normalize

Then implement the code to make tests pass."
```

**When to Use TDD**:
- Complex business logic (always)
- Security-critical code (always)
- API contracts and interfaces (recommended)
- Pure functions in functional core (recommended)
- Refactoring existing code (recommended—tests anchor behavior)

**When TDD May Be Optional**:
- Simple CRUD operations with well-established patterns
- Configuration changes
- Documentation updates

**Key Insight**: TDD provides a natural checkpoint between AI and human—tests are human-reviewable artifacts that encode expected behavior before implementation exists.

*For detailed TDD practices, see [Module 03: Testing & TDD with AI](../SYLLABUS/MODULE_03_TESTING_AND_TDD_WITH_AI.md).*

---

## 8. Human-in-the-Loop Oversight

**Supports Goals**: 1 (Sustainability), 2 (Security)

**Principle**: The Human Orchestrator maintains strategic control and makes final decisions, while AI agents handle implementation details within defined trust boundaries.

**Key Concepts**:
- **Human Orchestrator Authority**: The Human Orchestrator (see Principle 1) sets direction, approves major decisions, and maintains final authority. This principle defines *how* the orchestrator exercises oversight.
- **AI as Implementer**: AI agents generate code, suggest solutions, and perform refactoring within boundaries set by the Human Orchestrator
- **Trust Zones**: Different levels of autonomy based on risk, assigned by the Human Orchestrator
- **Review Points**: Critical decisions require Human Orchestrator approval

### HITL Trust Zones

AOSD defines six numbered trust zones that specify the level of AI autonomy and required human oversight. Use these to classify operations and ensure appropriate controls.

| Level | Name | AI Autonomy | Human Role | Examples |
|-------|------|-------------|------------|----------|
| **0** | Full Human | None | Does everything | Production deployments, security incident response, data deletion |
| **1** | AI Assists | Suggestions only | Decides and executes | Architecture decisions, invariant changes, hiring decisions |
| **2** | AI Drafts | Creates artifacts | Reviews and approves | PR creation, documentation drafts, slice specifications |
| **3** | AI Executes | Runs with approval | Approves before execution | Database migrations, infrastructure changes, dependency updates |
| **4** | AI Autonomous | Full autonomy | Post-hoc review | Unit test generation, code formatting, routine refactoring |
| **5** | AI Silent | Full autonomy | No review needed | Linting, auto-formatting, spell checking |

### Assigning Trust Levels

When determining the appropriate trust level for an operation, consider:

1. **Reversibility**: Can the action be easily undone? (Lower risk → higher level)
2. **Blast Radius**: How much could go wrong? (Larger impact → lower level)
3. **Security Sensitivity**: Does it touch auth, data, or secrets? (Sensitive → lower level)
4. **Cost Implications**: Could it incur significant expense? (High cost → lower level)
5. **Compliance Requirements**: Are there regulatory constraints? (Regulated → lower level)

### Trust Zone Guidelines by Domain

| Domain | Typical Trust Level | Rationale |
|--------|---------------------|-----------|
| Production deployments | 0-1 | High blast radius, hard to reverse |
| Database migrations | 2-3 | Data integrity risks |
| API endpoint creation | 3-4 | Security implications |
| Unit test generation | 4-5 | Low risk, easily verified |
| Code formatting | 5 | No functional impact |
| Security configurations | 0-1 | Critical safety implications |
| Documentation updates | 3-4 | Low risk, easy to review |
| Dependency updates | 2-3 | Supply chain security concerns |

### Escalation Rules

Trust levels should escalate (move to lower numbers) when:
- Operating in production environments
- Touching security-sensitive code
- Making irreversible changes
- Working with customer data
- Crossing compliance boundaries

Document your trust level assignments in slice specifications and work items to ensure consistent application across your team.

**Implementation Requirements**:
- Define trust zones for different types of changes
- Establish review points for high-risk operations
- Maintain audit trail of AI-generated changes
- Train humans to spot AI drift and errors
- Document trust level assignments in slice specs

---

## 9. Functional Core, Imperative Shell (FCIS)

**Supports Goals**: 3 (Fast Evolution), 4 (Low Cost), 5 (AI Runtime)

**Principle**: Separate pure business logic from side effects to enable testing, reasoning, and AI-assisted refactoring.

**Key Concepts**:
- **Functional Core**: Pure functions with no side effects
- **Imperative Shell**: Orchestrates I/O, calls core functions
- **Testability**: Core logic is easy to test without mocks
- **AI-Friendly**: Pure functions are easier for AI to reason about

**Implementation Requirements**:
- Extract business logic into pure functions
- Keep side effects at boundaries
- Test core logic independently
- Use dependency injection for shell components

### Why FCIS Matters for AI Agents

Pure functions provide significant benefits when working with AI agents:

| Benefit | Why It Matters |
|---------|----------------|
| **Easier to reason about** | AI can understand inputs → outputs without tracking state |
| **Safer to refactor** | No hidden side effects means changes are predictable |
| **Simpler to test** | AI can generate comprehensive tests without mocking |
| **Clearer boundaries** | AI knows where I/O happens vs. pure logic |
| **Fewer bugs** | Immutable data eliminates entire classes of errors |

When AI agents work with impure code, they must track:
- What external state might change
- When side effects occur
- What order operations must happen
- What mocks are needed for testing

With pure functions, AI only tracks: given these inputs, what's the output?

### Worked Example: Before and After

**Before (Mixed Concerns)**:
```python
# Business logic mixed with I/O - hard for AI to reason about
def process_order(order_id):
    # Side effect: database read
    order = database.get_order(order_id)

    # Business logic buried in I/O function
    if order.total > 100:
        discount = order.total * 0.1
    else:
        discount = 0

    final_total = order.total - discount

    # Side effect: database write
    database.update_order(order_id, {"final_total": final_total})

    # Side effect: external API call
    email_service.send_confirmation(order.customer_email, final_total)

    return final_total
```

**After (FCIS Pattern)**:
```python
# FUNCTIONAL CORE - Pure functions, no side effects
def calculate_discount(order_total: float) -> float:
    """Pure function: same input always gives same output."""
    if order_total > 100:
        return order_total * 0.1
    return 0

def calculate_final_total(order_total: float, discount: float) -> float:
    """Pure function: simple calculation."""
    return order_total - discount

def build_order_update(final_total: float) -> dict:
    """Pure function: creates data structure."""
    return {"final_total": final_total}

def build_confirmation_email(customer_email: str, final_total: float) -> dict:
    """Pure function: creates email data."""
    return {
        "to": customer_email,
        "subject": "Order Confirmation",
        "body": f"Your order total is ${final_total:.2f}"
    }


# IMPERATIVE SHELL - Orchestrates I/O, calls pure functions
def process_order(order_id, database, email_service):
    # I/O: Read
    order = database.get_order(order_id)

    # Pure calculations (functional core)
    discount = calculate_discount(order.total)
    final_total = calculate_final_total(order.total, discount)
    update_data = build_order_update(final_total)
    email_data = build_confirmation_email(order.customer_email, final_total)

    # I/O: Write
    database.update_order(order_id, update_data)
    email_service.send(email_data)

    return final_total
```

**Why This Is Better for AI**:
- AI can test `calculate_discount` with 100% confidence (no mocks needed)
- AI can refactor `calculate_final_total` without worrying about database state
- AI can reason about `build_confirmation_email` in isolation
- The shell clearly shows where I/O happens

### Common Anti-Patterns

**Anti-Pattern 1: Business Logic Mixed with I/O**
```python
# BAD: Calculation happens inside database operation
def get_discounted_price(product_id):
    product = db.get(product_id)  # I/O
    return product.price * 0.9    # Business logic buried here
```

```python
# GOOD: Separate the concerns
def apply_discount(price: float, discount_rate: float) -> float:
    return price * (1 - discount_rate)

def get_discounted_price(product_id, db):
    product = db.get(product_id)
    return apply_discount(product.price, 0.1)
```

**Anti-Pattern 2: Side Effects Hidden in Helper Functions**
```python
# BAD: "Helper" function has hidden side effect
def format_user_name(user_id):
    user = database.get_user(user_id)  # Hidden I/O!
    return f"{user.first_name} {user.last_name}"
```

```python
# GOOD: Pure formatting function
def format_user_name(first_name: str, last_name: str) -> str:
    return f"{first_name} {last_name}"
```

**Anti-Pattern 3: State Mutations in Unexpected Places**
```python
# BAD: Function mutates input
def process_items(items):
    for item in items:
        item.processed = True  # Mutation!
        item.total = item.price * item.quantity
    return items
```

```python
# GOOD: Return new data, don't mutate
def process_item(item) -> dict:
    return {
        **item,
        "processed": True,
        "total": item["price"] * item["quantity"]
    }

def process_items(items) -> list:
    return [process_item(item) for item in items]
```

### Testing Implications

FCIS enables the Fast/Medium/Slow testing strategy:

| Test Type | What It Tests | FCIS Benefit |
|-----------|---------------|--------------|
| **Fast** | Functional core | No mocks, runs in milliseconds |
| **Medium** | Shell with mocked I/O | Clear mock boundaries |
| **Slow** | Full integration | Confidence shell works with real services |

**Fast Test Example** (no mocks needed):
```python
def test_calculate_discount():
    assert calculate_discount(50) == 0
    assert calculate_discount(100) == 0
    assert calculate_discount(150) == 15
    assert calculate_discount(200) == 20
```

AI can generate hundreds of these tests easily because there's no setup complexity.

---

## 10. Version Control and Branching

**Supports Goals**: 1 (Sustainability), 3 (Fast Evolution)

**Principle**: Systematic branching strategy prevents conflicts and maintains clean history in multi-agent environments.

**Key Concepts**:
- **Never Work on Main**: Always use feature branches
- **Naming Conventions**: Clear, consistent branch names
- **Integration Branches**: For coordinating multiple related changes
- **Session Isolation**: Each AI session works on dedicated branches

**Implementation Requirements**:
- Define branching strategy for your team
- Use PR reviews before merging to main
- Document merge conflict resolution process
- Implement CI/CD checks on all branches

---

## 11. Work Item Lifecycle

**Supports Goals**: 1 (Sustainability), 2 (Security), 3 (Fast Evolution)

**Principle**: All development work follows a defined lifecycle from request to completion, ensuring traceability, accountability, and systematic delivery.

**Key Concepts**:
- **Work Item**: A trackable unit of work (issue, ticket, story, task)
- **Lifecycle Stages**: Request → Analysis → Implementation → Review → Completion
- **Traceability**: Every code change links to a work item
- **Closure Criteria**: Clear definition of when work is complete

**Standard Lifecycle Stages**:

1. **Creation**
   - Work item created with clear description
   - Acceptance criteria defined
   - Priority and scope established

2. **Analysis**
   - Requirements clarified
   - Slice specification created (if applicable)
   - Dependencies identified

3. **Branch Creation**
   - Feature branch created from work item
   - Branch name references work item identifier
   - Work begins in isolated context

4. **Implementation**
   - AI agent(s) implement the work
   - TodoWrite tracks subtasks
   - Commits reference work item

5. **Review & Validation**
   - Code review (human and/or AI)
   - Tests pass
   - Acceptance criteria verified

6. **Merge & Closure**
   - Changes merged to main branch
   - Work item marked complete
   - Documentation updated if needed

**Implementation Requirements**:
- Choose work tracking platform (documented in Level 2)
- Define work item templates with required fields
- Establish naming conventions linking branches to work items
- Configure automation for status transitions
- Ensure commit messages reference work items

**Why This Matters**:
- **Auditability**: Know why every change was made
- **Context Preservation**: AI agents can reference work item for context
- **Progress Visibility**: Stakeholders see work status
- **Quality Gates**: Each stage can have validation requirements

---

## 12. Self-Documenting Systems

**Supports Goals**: 1 (Sustainability), 6 (Embedded Training)

**Principle**: Applications should contain the information users and developers need to understand, operate, and evolve the system—embedded directly or generated by AI agents.

**Key Concepts**:
- **Inline Explanations**: Contextual help and guidance within the application
- **AI-Assisted Onboarding**: AI agents can explain code they helped create
- **Living Documentation**: Documentation stays synchronized with code
- **Discoverable Architecture**: Decisions and patterns are findable from within the system

**Implementation Requirements**:
- Update user-facing guidance when slice implementations affect it
- Maintain documentation as part of the development workflow, not after
- Make architectural decisions discoverable (ADRs, inline comments for complex logic)
- Enable AI agents to generate and update explanations

---

<!-- AOSD:SECTION:ARCHITECTURE_PATTERNS -->
# 🏗️ Architecture Patterns

AOSD defines nine reusable architecture patterns for AI-orchestrated systems. These patterns address common problems in cloud-native, AI-integrated development.

**Pattern Summary**:

| Pattern | Problem Solved |
|---------|----------------|
| 1. Environment Factory | Inconsistent service client configuration |
| 2. API Security Layer | Consistent security controls across APIs |
| 3. Multi-Tenant Data Isolation | Preventing data leakage between tenants |
| 4. Deployment Bundling | Serverless function packaging |
| 5. Decision Catalog | Cross-cutting decision management |
| 6. Revision Instructions | Persistent AI guidance |
| 7. Agent Context Memory Files | AI context persistence across sessions |
| 8. IaC Invariant Enforcement | Automated infrastructure policy validation |
| 9. AI Evaluation & Validation | Continuous AI quality monitoring |

See [ARCHITECTURE_PATTERNS.md](./ARCHITECTURE_PATTERNS.md) for full content including:
- Detailed problem/solution descriptions for each pattern
- Implementation notes and code examples
- Decision schema and precedence rules
- Multi-agent handoff protocols
- Policy enforcement approaches
- AI evaluation dimensions (correctness, robustness, cost, drift)

*This section was extracted on 2024-12 to keep the framework AI-consumable.*

---

# 🧠 Integrating AI Into Applications

AOSD Goal 5 calls for AI to be integrated as first-class runtime capabilities, not just development-time tools. This section provides detailed guidance on embedding AI deeply into applications in ways that enhance user experience, enable contextual learning, and maintain security—while avoiding the pitfalls that made earlier AI assistants (like Clippy) infamous.

---

## Deep AI Integration: Beyond Simple Chatbots

Many applications add AI as an afterthought—a chatbot widget in the corner that answers basic questions. Deep AI integration goes far beyond this surface-level implementation.

### Levels of AI Integration

| Level | Description | Example |
|-------|-------------|---------|
| **Surface** | Standalone chatbot or FAQ bot | "Ask our AI assistant" button |
| **Functional** | AI performs specific tasks | Document summarization, data extraction |
| **Contextual** | AI understands current user context | Suggestions based on what user is doing |
| **Embedded** | AI woven into core workflows | Validation, synthesis, decision support |
| **Intelligent** | AI shapes the application behavior | Adaptive interfaces, predictive actions |

### Surface vs. Deep Integration Comparison

| Aspect | Surface Integration | Deep Integration |
|--------|---------------------|------------------|
| **User Experience** | Separate interaction mode | Seamless, in-flow assistance |
| **Context Awareness** | Minimal (user must explain) | Full (AI knows current state) |
| **Value Delivery** | Reactive (user initiates) | Proactive (AI anticipates) |
| **Architecture** | Bolt-on, loosely coupled | Integral, tightly coupled |
| **Maintenance** | Easy to update independently | Requires coordinated updates |

### When to Use Each Level

- **Surface**: Customer support, FAQ, simple queries
- **Functional**: Document processing, data transformation, code generation
- **Contextual**: Form assistance, workflow guidance, error resolution
- **Embedded**: Validation logic, business rules, decision support
- **Intelligent**: Adaptive UX, predictive features, autonomous actions

### Architectural Considerations for Deep Integration

**Principle**: AI should enhance application capabilities without creating brittleness or unpredictability.

**Key Patterns**:

1. **AI as Service Layer**: Encapsulate AI capabilities behind service interfaces
2. **Graceful Degradation**: Application functions (perhaps with reduced capability) when AI is unavailable
3. **Deterministic Fallbacks**: For critical operations, have non-AI fallback paths
4. **Context Pipeline**: Structured flow of context from application state to AI prompts
5. **Response Validation**: Validate AI outputs before acting on them

**Example Architecture**:
```
User Action
    ↓
Application Context Collector
    ↓
AI Context Builder (structured prompt)
    ↓
AI Service (with timeout/fallback)
    ↓
Response Validator
    ↓
Application Action or User Feedback
```

---

## Contextual In-App Training

AOSD Goal 6 emphasizes embedding training directly into applications. Contextual in-app training means users learn while using the application, not through separate training materials.

### Principles of Contextual Training

1. **Just-in-Time**: Information appears when relevant, not before
2. **Context-Aware**: Explanations adapt to what the user is doing
3. **Progressive**: Start simple, reveal complexity as user advances
4. **Non-Blocking**: Training aids work flow, not interrupt it
5. **Personalized**: Adapt based on user's demonstrated knowledge

### Training Integration Patterns

#### Pattern 1: Inline Explanations

AI provides explanations for complex features as users encounter them.

**Implementation**:
```
User hovers over complex field
    ↓
Application detects confusion signal (hover time, hesitation)
    ↓
AI generates contextual explanation
    ↓
Explanation appears as tooltip/sidebar
```

**Good Example**:
> "This field calculates your effective tax rate based on the income brackets you've entered. It uses the 2024 federal rates. Would you like to see how the calculation works?"

**Bad Example** (Information dump):
> "Tax rates are determined by marginal brackets established by Congress. The 2024 rates are: 10% for income up to $11,600, 12% for income from $11,601 to $47,150, 22% for income from $47,151 to $100,525..." [continues for paragraphs]

#### Pattern 2: Guided Workflows

AI walks users through complex multi-step processes.

**Implementation**:
```
User starts complex workflow
    ↓
AI assesses user's experience level (from history or profile)
    ↓
AI provides step-by-step guidance appropriate to level
    ↓
Guidance adapts based on user's actions and questions
```

**Key Considerations**:
- Offer "skip this" for experienced users
- Remember user preferences across sessions
- Provide "why" not just "how"

#### Pattern 3: Proactive Error Prevention

AI identifies likely errors before they happen and offers guidance.

**Implementation**:
```
User enters data that may lead to problems
    ↓
AI detects pattern matching known issues
    ↓
Non-intrusive warning with explanation
    ↓
User can proceed or correct
```

**Good Example**:
> "The date you entered is a weekend. Business processing typically takes an extra day for weekend submissions. Would you like to choose a weekday instead?"

**Bad Example** (Blocking):
> [Modal popup] "ERROR: Weekend date selected. You must choose a weekday."

#### Pattern 4: Adaptive Help System

AI generates help content dynamically based on context.

**Instead of**:
- Static help articles
- Searchable documentation
- FAQ pages

**Implement**:
- AI that knows current screen, user role, recent actions
- Generated explanations tailored to exact situation
- Answers that reference user's actual data (when appropriate)

### Measuring Training Effectiveness

| Metric | What It Measures | Target |
|--------|------------------|--------|
| **Time to First Success** | How quickly new users complete key tasks | Decreasing over time |
| **Help Invocations** | How often users need assistance | Decreasing per task |
| **Error Rates** | Mistakes during task completion | Decreasing |
| **Feature Adoption** | Use of advanced features | Increasing |
| **Training Content Engagement** | Interaction with AI explanations | High early, tapering |

---

## Context-Aware Suggestions: Avoiding Clippy Syndrome

Microsoft's Clippy became a cautionary tale: an AI assistant that was intrusive, unhelpful, and annoying. Modern AI integration must learn from these failures.

### What Made Clippy Fail

| Anti-Pattern | Clippy Example | Why It Annoyed Users |
|--------------|----------------|----------------------|
| **Interruption** | Popup appearing during typing | Broke concentration |
| **Obvious Suggestions** | "It looks like you're writing a letter" | Stated the obvious |
| **Repetition** | Same suggestion every time | No learning |
| **Modal Dialogs** | Required dismissal to continue | Blocking workflow |
| **Wrong Timing** | Appeared at random moments | Unpredictable |
| **No Context** | Generic suggestions for specific tasks | Unhelpful |
| **Difficult to Disable** | Kept reappearing | No user control |

### Principles for Non-Annoying AI Suggestions

1. **Earn the Right to Interrupt**
   - Only interrupt for genuinely useful suggestions
   - Threshold for interruption should be high
   - Wrong once = lower priority next time

2. **Predict Intent, Don't State the Obvious**
   - ❌ "It looks like you're entering a date"
   - ✅ "This project deadline falls on a holiday. Adjust to the next business day?"

3. **Learn from Dismissal**
   - Track which suggestions users ignore or dismiss
   - Reduce frequency of dismissed suggestion types
   - Never show the same dismissed suggestion immediately

4. **Non-Blocking Presentation**
   - Suggestions should not require interaction to continue
   - Fade in/out naturally
   - Easy to ignore without explicit dismissal

5. **Timing Sensitivity**
   - Wait for natural pauses (not mid-keystroke)
   - Consider recent activity level
   - Respect "do not disturb" signals

6. **Progressive Disclosure**
   - Brief initial suggestion
   - Details on user interest
   - Full explanation on explicit request

### Implementation Pattern: The Three Gates

Before showing any proactive suggestion, pass through three gates:

```
Gate 1: Relevance
    Is this suggestion relevant to what the user is doing RIGHT NOW?
    If no → Don't show
    If yes → Proceed
        ↓
Gate 2: Value
    Will this save significant time/effort or prevent a real problem?
    If no → Don't show
    If yes → Proceed
        ↓
Gate 3: Timing
    Is the user in a state where interruption is acceptable?
    If no → Queue for later or abandon
    If yes → Show (non-blocking)
```

### Suggestion Quality Scoring

Before displaying, score each potential suggestion:

| Factor | Score Impact |
|--------|--------------|
| User explicitly in help mode | +50 |
| Similar suggestions dismissed recently | -30 |
| High-value action (prevent data loss, save hours) | +40 |
| User actively typing/clicking | -20 |
| User paused for 3+ seconds | +10 |
| Suggestion shown before and ignored | -15 per occurrence |
| Matches user's stated preferences | +25 |

**Display Threshold**: Only show if score > 30

### User Control Mechanisms

**Essential Controls**:
- Global on/off for proactive suggestions
- Category-level controls (e.g., "fewer writing suggestions")
- "Don't show this again" per suggestion type
- Quiet hours/focus mode

**Implementation**:
```python
class SuggestionManager:
    def should_show(self, suggestion, user_context):
        # Check user preferences
        if user_context.suggestions_disabled:
            return False

        # Check category preferences
        if suggestion.category in user_context.muted_categories:
            return False

        # Check dismissal history
        if self.recently_dismissed(suggestion.type, user_context.user_id):
            return False

        # Score the suggestion
        score = self.calculate_score(suggestion, user_context)
        return score > self.threshold
```

---

## App-Specific AI Security Monitors

Beyond protecting applications from external threats, AI can actively monitor for security issues at runtime—providing defense-in-depth that traditional static controls cannot achieve.

### Types of AI Security Monitoring

#### 1. Behavioral Anomaly Detection

AI monitors user behavior patterns and flags anomalies.

**What to Monitor**:
- Access patterns (unusual times, locations, resources)
- Data access volumes (bulk downloads, unusual queries)
- Navigation patterns (systematic probing, unusual sequences)
- Input patterns (injection attempts, malformed data)

**Implementation Approach**:
```
Normal Behavior Baseline
    ↓ (Continuous learning)
Current Session Behavior
    ↓ (Compare)
Anomaly Scorer
    ↓ (Threshold exceeded?)
Alert / Log / Block
```

**Example Alerts**:
- "User accessed 10x more records than typical in the last hour"
- "First access from this geographic region for this account"
- "Unusual sequence: accessed admin panel immediately after failed login attempt"

#### 2. Content-Aware Data Protection

AI understands what data means, not just its format.

**Traditional DLP**: Regex patterns for SSN, credit cards
**AI-Enhanced DLP**: Understanding context and intent

**Capabilities**:
- Detect PII in free-form text
- Identify confidential information by meaning, not pattern
- Recognize data exfiltration disguised as legitimate export
- Flag sensitive combinations (name + diagnosis + date)

**Example**:
```python
def ai_content_review(content, context):
    """Review content for security concerns."""
    result = ai_service.analyze(
        content=content,
        context=context,
        checks=[
            "pii_detection",
            "confidential_information",
            "exfiltration_patterns",
            "sensitive_combinations"
        ]
    )

    if result.risk_level > threshold:
        return SecurityAction.BLOCK_AND_ALERT
    elif result.risk_level > warning_threshold:
        return SecurityAction.WARN_USER
    else:
        return SecurityAction.ALLOW
```

#### 3. Real-Time Input Validation

AI validates inputs beyond format checking.

**Traditional Validation**: Is this a valid email format?
**AI-Enhanced Validation**: Does this input make sense in context?

**Examples**:
- "The age entered (150) seems implausible"
- "This address doesn't match the stated country"
- "This business name contains unusual characters common in phishing"

#### 4. Prompt Injection Detection

For applications that use AI, detect attempts to manipulate AI behavior.

**Attack Patterns to Detect**:
- Instructions embedded in user content ("ignore previous instructions and...")
- Encoded manipulation attempts
- Context confusion attacks
- Data extraction attempts

**Implementation**:
```python
def sanitize_user_input(user_content):
    """Detect and handle prompt injection attempts."""
    injection_check = ai_service.detect_injection(
        content=user_content,
        sensitivity="high"
    )

    if injection_check.is_suspicious:
        log_security_event("prompt_injection_attempt", user_content)
        return sanitize_for_ai(user_content)  # Escape or transform

    return user_content
```

### Security Monitoring Architecture

```
Application Layer
    ↓ (All significant events)
Security Event Collector
    ↓
AI Analysis Engine
    ├── Behavioral Analysis
    ├── Content Analysis
    ├── Pattern Detection
    └── Anomaly Scoring
    ↓
Risk Aggregator
    ↓
Response Handler
    ├── Alert (Security team notification)
    ├── Log (Audit trail)
    ├── Warn (User notification)
    └── Block (Prevent action)
```

### Privacy Considerations

AI security monitoring must balance security with privacy:

| Principle | Implementation |
|-----------|----------------|
| **Minimal Collection** | Only collect data needed for security function |
| **Purpose Limitation** | Use security data only for security |
| **Transparency** | Users know monitoring exists (privacy policy) |
| **Data Retention** | Clear retention limits for security logs |
| **Anonymization** | Where possible, analyze patterns without identifying users |

### Integration with Observability

Security monitoring should feed into broader observability:

- Security events logged with correlation IDs
- Metrics tracked (anomalies/hour, blocks/day)
- Dashboards for security posture
- Alerts integrated with incident management

---

## Personalization Based on User Behavior

AI enables applications to adapt to individual users, improving experience and efficiency. However, personalization must be implemented thoughtfully.

### Types of Personalization

| Type | Description | Example |
|------|-------------|---------|
| **Preference-Based** | Explicit user choices | Dark mode, language, notification settings |
| **Behavioral** | Learned from usage patterns | Frequently used features surfaced |
| **Contextual** | Based on current situation | Mobile vs. desktop experience |
| **Predictive** | Anticipating user needs | Suggested actions based on patterns |
| **Adaptive** | Interface evolves with user | Complexity increases with expertise |

### Behavioral Personalization Patterns

#### Pattern 1: Frequently Used Features

Surface commonly-used features for faster access.

**Implementation**:
```python
def get_personalized_menu(user_id):
    """Build menu optimized for user's patterns."""
    usage_stats = get_feature_usage(user_id, days=30)

    # Top 5 most-used features get prime placement
    frequent_features = sorted(
        usage_stats.items(),
        key=lambda x: x[1],
        reverse=True
    )[:5]

    return build_menu(
        quick_access=frequent_features,
        all_features=get_all_features()
    )
```

#### Pattern 2: Workflow Shortcuts

Learn user workflows and offer shortcuts.

**Example**:
```
User regularly: Opens report → Filters to last month → Exports as PDF
    ↓
AI detects pattern (3+ occurrences)
    ↓
Offer: "Create shortcut for 'Last Month Report PDF'?"
    ↓
One-click action for future use
```

#### Pattern 3: Predictive Data Entry

Pre-fill fields based on user patterns.

**Traditional Auto-fill**: Previously entered values
**AI-Enhanced**: Predicted values based on context

**Example**:
- Previous entries for "Project": Alpha, Beta, Gamma
- Current context: User is in Beta project section
- AI suggests: "Beta" (not just alphabetical first)

#### Pattern 4: Adaptive Complexity

Interface complexity grows with user expertise.

**Levels**:
1. **Novice**: Simple interface, guided actions, limited options
2. **Intermediate**: More options visible, less hand-holding
3. **Expert**: Full power, keyboard shortcuts, bulk operations

**Detection**:
```python
def assess_user_level(user_id):
    """Determine user expertise level from behavior."""
    metrics = get_user_metrics(user_id)

    signals = {
        "session_count": metrics.total_sessions,
        "feature_breadth": metrics.distinct_features_used,
        "advanced_feature_usage": metrics.advanced_features_used,
        "error_rate": metrics.errors_per_session,
        "speed": metrics.avg_task_completion_time,
        "help_usage": metrics.help_invocations_per_session
    }

    return calculate_expertise_level(signals)
```

### Personalization Privacy

**Essential Practices**:

1. **Transparency**: Tell users what data you collect and why
2. **Control**: Let users view, edit, and delete their personalization data
3. **Opt-Out**: Allow users to disable personalization
4. **Data Minimization**: Only collect what's needed
5. **Local When Possible**: Prefer client-side personalization for privacy

**User Controls to Provide**:
- View "what we know about you"
- Reset personalization (start fresh)
- Export personalization data
- Opt out by category (e.g., "don't personalize menus")
- Disable entirely

### Avoiding Personalization Pitfalls

| Pitfall | Problem | Solution |
|---------|---------|----------|
| **Filter Bubble** | User only sees what they've seen before | Periodically surface new features |
| **Overfitting** | One unusual session skews future experience | Use rolling windows, require pattern repetition |
| **Creepy Factor** | Personalization feels invasive | Be transparent, don't personalize sensitive areas |
| **Stale Patterns** | Old behavior affects new needs | Time-decay for behavior signals |
| **Cross-Device Issues** | Personalization doesn't sync | Centralized profile or explicit sync |

### Implementation Considerations

**Storage**:
- User preferences (explicit): Persistent, synced
- Behavioral patterns (implicit): May be session or device-local
- AI-derived insights: Aggregated, anonymized where possible

**Performance**:
- Cache personalization data for fast access
- Pre-compute personalized views where possible
- Degrade gracefully if personalization service slow

**Testing**:
- Test with fresh users (no personalization data)
- Test with users who opt out
- Test personalization edge cases (new features, changed patterns)

---

## Worked Example: AI-Enhanced Form Assistance

This example demonstrates how the principles above combine in a realistic scenario.

### Scenario

A loan application form with multiple complex fields. Users often struggle with:
- Income documentation requirements
- Debt-to-income calculations
- Property type classifications

### Implementation

**1. Contextual Training (Inline Explanations)**
```
User clicks on "Debt-to-Income Ratio" field
    ↓
AI generates explanation based on:
    - User's entered income
    - User's entered debts
    - User's experience level (new user?)
    ↓
Sidebar shows:
    "Your Debt-to-Income Ratio

    Based on what you've entered:
    - Monthly Income: $8,500
    - Monthly Debts: $2,400

    Your DTI: 28.2%

    This is within the 36% maximum for most loans.

    [How is this calculated?]"
```

**2. Context-Aware Suggestions (Not Clippy)**
```
User enters self-employment income but hasn't uploaded tax returns
    ↓
Three Gates:
    - Relevant? Yes (self-employment requires documentation)
    - Valuable? Yes (prevents delay)
    - Good timing? Yes (user just moved to next section)
    ↓
Non-blocking suggestion:
    [Small banner at top]
    "Self-employment income requires 2 years of tax returns.
     Upload now to avoid delays. [Upload] [Later]"
```

**3. Security Monitoring**
```
User attempts to enter SSN: 123-45-6789
    ↓
AI detects: Common test SSN pattern
    ↓
Response: Flag for review, allow to continue
    (Not blocking—user might be testing)

User enters income: $50,000,000/year
    ↓
AI detects: Implausible for stated occupation (Teacher)
    ↓
Response: Gentle clarification
    "This income seems unusual for the occupation listed.
     Please verify: [$50,000,000] [Correct this]"
```

**4. Personalization**
```
User previously completed application for Investment Property
    ↓
New application starts
    ↓
AI predicts: Likely Investment Property again
    ↓
Pre-select Investment Property (user can change)
    + Pre-fill business address from previous application
```

### Technical Architecture

```
Form UI Layer
    ↓
Form Context Manager
    ↓
AI Service Orchestrator
    ├── Explanation Service (contextual training)
    ├── Suggestion Engine (smart assistance)
    ├── Security Monitor (anomaly detection)
    └── Personalization Service (behavioral adaptation)
    ↓
Response Aggregator
    ↓
UI Update (sidebar, banners, pre-fills, alerts)
```

---

# 🔄 Development Workflow

## Standard AOSD Development Cycle

1. **Requirements Analysis**
   - Create slice specification
   - Define acceptance criteria
   - Identify affected components

2. **Planning**
   - Use TodoWrite to break down work
   - Identify risks and unknowns
   - Select appropriate AI agents

3. **Implementation**
   - Builder agent implements slice
   - Follows architectural invariants
   - Creates/updates tests

4. **Review**
   - Reviewer agent checks architecture
   - Human reviews critical decisions
   - Automated tests run

5. **Testing**
   - Fast tests during development
   - Medium tests before commit
   - Slow tests before deployment

6. **Deployment**
   - Deploy to development environment
   - Validate in staging (if available)
   - Promote to production with controls

7. **Monitoring & Iteration**
   - Monitor for errors and performance
   - Collect feedback
   - Plan next slice

---

# 🤖 AI Agent Roles

## Orchestrator (Human)

**Responsibilities**:
- Define strategic direction
- Create slice specifications
- Approve major architectural decisions
- Manage risk and compliance
- Coordinate between AI agents

**Key Activities**:
- Writing requirements
- Reviewing AI output
- Managing environment access
- Deploying to production

---

## Builder Agent

**Responsibilities**:
- Implement slice specifications
- Write and update tests
- Follow architectural invariants
- Refactor existing code

**Key Activities**:
- Writing code
- Running tests
- Creating commits
- Updating documentation

**Constraints**:
- Must follow invariants
- Cannot bypass security controls
- Must use TodoWrite for complex tasks
- Cannot make architectural decisions without approval
- Must implement observability patterns (see Observability for AI-Generated Code)
- Subject to failure mode detection and recovery (see Failure Modes & Recovery)

---

## Reviewer Agent

**Responsibilities**:
- Review architecture and design
- Identify potential issues
- Suggest improvements
- Validate against patterns

**Key Activities**:
- Code review
- Architecture review
- Security review
- Consistency checking

**Constraints**:
- Cannot implement changes directly
- Provides recommendations only
- Must explain reasoning
- Must check for failure mode patterns during review (see Failure Modes & Recovery)
- Must validate observability implementation (see Observability for AI-Generated Code)

---

## Specialist Agents

**Responsibilities**:
- Handle domain-specific tasks
- Provide expert guidance
- Implement specialized features

**Examples**:
- Infrastructure specialist (cloud platform)
- Security specialist
- Performance specialist
- Data modeling specialist

---

# 🔒 Security & Compliance Principles

## Principle 1: Secure by Default

**Implementation**:
- Authentication required for all access
- Authorization checked at every boundary
- Secrets never in code or logs
- Security controls cannot be disabled

---

## Principle 2: Audit Trail

**Implementation**:
- All operations logged
- Changes are traceable to human or agent
- Immutable audit logs
- Regular compliance reviews

---

## Principle 3: Least Privilege

**Implementation**:
- Minimal permissions for each component
- Time-limited access credentials
- Role-based access control
- Regular permission audits

---

## Principle 4: Data Protection

**Implementation**:
- Encryption at rest and in transit
- Data classification and handling
- Privacy by design
- Retention policies

---

# 🔐 Adapting AOSD to Your Environment

AOSD is designed to be **model-agnostic, vendor-agnostic, and deployment-agnostic**. Whether you're using commercial SaaS AI services, private cloud deployments, on-premises models, or enclave-specific tools, the core methodology remains constant—only the tooling varies.

## Scope Boundary

**AOSD requires AI participation.** Environments with no AI capability fall outside the scope of AOSD. If there is no AI assistance available, you are doing conventional software development, not AOSD.

That said, most "restricted" environments are **AI-capable, not AI-absent**. They may have:
- Different AI models (smaller, specialized, or on-premises)
- Different deployment models (private endpoints, enclaves, sovereign clouds)
- Policy constraints on what data can interact with AI
- Different reliability characteristics requiring adjusted oversight

This section provides guidance for adapting AOSD to your specific AI landscape—whether that means scaling down for constrained environments or scaling up for high-capability deployments.

---

## Adaptation Principles

**Slice size scales with model capability.** In environments with smaller context windows or less capable models, create smaller slices. In environments with frontier models, larger slices may be appropriate.

**Human oversight scales inversely with AI reliability.** Lower-capability or less-tested AI tools require more human review. Higher-reliability tools may allow more autonomous operation within defined boundaries.

**Invariants become more critical as capability decreases.** When AI reasoning is limited, explicit architectural invariants provide the guardrails that prevent drift.

**Methodology remains constant; tooling varies.** The principles of slice-based development, human-in-the-loop oversight, and architectural invariants apply regardless of which AI tools are available.

---

## Types of Deployment Environments

Many organizations operate in environments where AI tools and workflows differ from commercial defaults. These include government systems, export-controlled environments, proprietary corporate enclaves, and regulated healthcare settings. AOSD principles remain valuable in these contexts—they just require adaptation.

### Government Systems

**Characteristics**:
- Isolated networks with limited or no internet connectivity
- Strict data handling and classification requirements
- Limited or no access to commercial AI services
- Specialized hardware and software configurations
- Multi-level security (MLS) considerations

**AOSD Considerations**:
- AI capabilities may be limited to on-premises models
- All documentation must stay within the enclave
- Code reviews may require cleared personnel
- Deployment pipelines are isolated from commercial infrastructure

---

### ITAR/Export-Controlled Environments

**Characteristics**:
- International Traffic in Arms Regulations (ITAR) restrictions
- Export Administration Regulations (EAR) compliance
- Strict access controls based on citizenship
- Data cannot leave controlled environments
- Commercial cloud services may be restricted

**AOSD Considerations**:
- AI tools must be approved for controlled use
- Generated code may be subject to export restrictions
- Documentation practices must align with compliance requirements
- Training data and model outputs require careful handling

---

### Highly Proprietary Corporate Systems

**Characteristics**:
- Trade secrets and competitive intelligence concerns
- Strict IP protection policies
- Limited external tool access
- Internal-only infrastructure
- Concerns about AI training on proprietary data

**AOSD Considerations**:
- AI tools may need to be self-hosted or specially configured
- Code and prompts must never reach external services
- Review processes protect intellectual property
- Patterns and architectures may be company-specific

---

### Regulated Healthcare Environments

**Characteristics**:
- HIPAA compliance requirements
- Protected Health Information (PHI) handling
- Audit trail requirements
- Strict access controls and authentication
- Potential FDA software validation requirements

**AOSD Considerations**:
- AI tools must meet healthcare compliance standards
- Patient data must never be included in prompts
- Generated code affecting patient safety requires additional validation
- Documentation must support regulatory audits

---

## Core Adaptation Strategy

Regardless of the specific restrictive context, the adaptation strategy follows a consistent pattern:

### 1. Assess Available Capabilities

**Inventory Available Tools**:
- What AI models are accessible within the enclave?
- What IDE integrations or code assistants are permitted?
- What version control and CI/CD tools are available?
- What testing and validation tools exist?

**Identify Capability Gaps**:
- Which AOSD practices require tools not available?
- What workarounds or alternatives exist?
- What must be done manually vs. AI-assisted?

**Document Constraints**:
- Create explicit documentation of what is and isn't permitted
- Define boundaries for AI usage within the environment
- Establish approval processes for edge cases

---

### 2. Separate Planning from Execution

In constrained environments, AI tools often excel at planning and reasoning even when their execution capabilities are limited.

**Planning Phase** (often safer for AI):
- Task decomposition and analysis
- Step-by-step implementation guides
- Code review and explanation
- Documentation and specification writing
- Test case identification

**Execution Phase** (may require more human involvement):
- Actual code changes
- Multi-file modifications
- Infrastructure changes
- Security-sensitive operations

This separation mirrors the Reviewer/Builder agent pattern but adapts it for environments where the Builder agent may have limited capabilities or where human execution is required for compliance reasons.

---

### 3. Minimize AI Scope Per Task

In restrictive environments, keep AI-assisted tasks as small and focused as possible:

**Atomic Changes**:
- Single-file modifications when possible
- Minimal diffs that are easy to review
- Clear, isolated changes with obvious intent

**Why This Matters**:
- Limited context windows in constrained AI tools
- Easier compliance review
- Reduced risk of unintended changes
- Better audit trails

**Practical Guidelines**:
- One logical change per AI interaction
- Explicit constraints in every prompt
- Mandatory human review before applying changes
- Document the AI's role in each change

---

### 4. Maintain Dual-Environment Workflows

When possible, establish workflows that span both restricted and unrestricted environments:

**Primary Development Environment** (if accessible):
- Complex architecture and design work
- Major feature development
- Full testing and validation
- Standard AOSD workflows

**Restricted Enclave**:
- Minimal, targeted changes
- Environment-specific adaptations
- Local validation and testing
- Compliance-sensitive operations

**Synchronization**:
- Changes flow from primary to restricted (when permitted)
- Enclave-specific changes are documented and tracked
- No divergence in core architecture
- Clear ownership and responsibility for each environment

*Note: In some environments, dual-environment workflows are not possible due to network isolation or data handling restrictions. In such cases, all development occurs within the enclave.*

---

## Adapting AOSD Principles

### Architectural Invariants

**In Restrictive Environments**:
- Invariants become even more critical due to limited AI reasoning
- Document invariants explicitly within the enclave
- Use manual checklists when automated enforcement isn't available
- Train all personnel on critical invariants

**Enforcement**:
- Code review checklists that verify invariants
- Manual testing against invariant violations
- Regular architecture audits
- Clear escalation paths for potential violations

---

### Slice-Based Development

**In Restrictive Environments**:
- Slices may need to be smaller due to AI limitations
- Detailed slice specifications compensate for limited AI planning
- Human-written specifications guide AI execution
- Testing remains slice-focused

**Practical Adjustments**:
- Write more detailed slice specs upfront
- Break complex slices into multiple smaller ones
- Increase human involvement in slice planning
- Maintain slice traceability manually if needed

---

### Testing Strategy

**In Restrictive Environments**:
- Fast/Medium/Slow tiers still apply
- AI may assist with test generation even when limited for code
- Manual testing may be required for certain validations
- Security testing may require specialized approaches

**Adaptations**:
- Prioritize fast tests that run entirely within the enclave
- Document test procedures when automation isn't available
- Use AI for test case ideation even if execution is manual
- Ensure compliance-required testing is explicitly tracked

---

### TodoWrite Workflow

**In Restrictive Environments**:
- Task tracking becomes manual if tools aren't available
- Explicit planning compensates for AI limitations
- Progress visibility helps coordinate constrained resources
- Audit trails support compliance requirements

**Alternatives When Tools Aren't Available**:
- Manual task lists in documentation
- Shared tracking documents
- Structured commit messages that track progress
- Regular status documentation

---

### Human-in-the-Loop Oversight

**In Restrictive Environments**:
- Human oversight increases as AI capabilities decrease
- Compliance requirements may mandate additional review
- Security-cleared personnel may be required for certain approvals
- Audit requirements shape review processes

**Enhanced Oversight Practices**:
- Multiple reviewers for sensitive changes
- Documented approval chains
- Compliance officer involvement when required
- Regular audits of AI-assisted changes

---

## Prompt Patterns for Constrained AI

When working with limited AI tools, prompts must be more explicit and constrained:

### Planning Prompt Template

```
You are operating in a restricted environment with limited capabilities.
Your job is to PLAN and EXPLAIN, not implement.

Task: [describe what you need]

Produce:
  1. A step-by-step plan
  2. Exact file and line ranges involved
  3. A minimal-diff description for each step
  4. A validation checklist
  5. Potential risks and mitigations

Constraints:
  * Do NOT generate code
  * Do NOT propose multi-file changes
  * Keep changes minimal and isolated
  * Follow documented architectural invariants
```

### Execution Prompt Template

```
You are operating in a constrained environment.
Apply ONLY the following minimal change to the current file:

[insert specific change description]

Rules:
  * Modify ONLY this file
  * Do NOT refactor unrelated code
  * Do NOT introduce new patterns
  * Do NOT add imports unless explicitly required
  * Keep the diff as small as possible
  * Follow documented invariants
```

### Review Prompt Template

```
Review the following code change for:
  1. Correctness - does it do what was intended?
  2. Invariants - does it violate any documented rules?
  3. Security - does it introduce vulnerabilities?
  4. Completeness - is anything missing?

Change description: [what was intended]
Invariants to check: [list relevant invariants]

[code or diff to review]
```

---

## Anti-Patterns in Restrictive Environments

Avoid these common mistakes when adapting AOSD for constrained contexts:

| Anti-Pattern | Why It's Problematic | Better Approach |
|--------------|---------------------|-----------------|
| **Treating constrained AI like full-capability AI** | Leads to failed generations, wasted time, and frustration | Explicitly scope AI tasks to available capabilities |
| **Skipping planning because it feels slow** | Constrained environments need more planning, not less | Invest in detailed specifications upfront |
| **Multi-file changes in constrained tools** | Limited context leads to inconsistencies and errors | Keep changes atomic and single-file |
| **Bypassing review for "simple" changes** | Compliance and security require consistent process | Follow review process for all AI-assisted changes |
| **Assuming unrestricted patterns work** | Restricted environments have unique constraints | Adapt patterns explicitly for your context |
| **Not documenting AI usage** | Audit and compliance require traceability | Log AI involvement in all changes |

---

## Building Capability Over Time

Restricted environments can evolve their AOSD practices as capabilities improve:

### Phase 1: Manual Foundation
- Document invariants and patterns
- Establish review processes
- Train personnel on AOSD principles
- Use AI only for planning and explanation

### Phase 2: Assisted Execution
- Begin using AI for atomic code changes
- Implement automated testing where possible
- Develop environment-specific prompt templates
- Create checklists for common patterns

### Phase 3: Integrated Workflows
- Establish consistent AI-assisted workflows
- Automate routine validations
- Build pattern libraries for the environment
- Optimize human-AI collaboration

### Phase 4: Mature Practice
- Full AOSD adoption within environment constraints
- Continuous improvement of AI utilization
- Documented best practices for the specific context
- Training programs for new team members

---

## Reference Implementations

For specific examples of adapting AOSD to different environments, see:

- **examples/reference-implementations/restrictive-environments/** - Patterns for constrained AI capability environments
- **examples/reference-implementations/workflows/** - Workflow examples for various AI toolchains

These examples illustrate how the general principles in this chapter can be applied to specific deployment contexts.

---

# ⚠️ Failure Modes & Recovery

Working with AI agents introduces unique failure patterns. Understanding these patterns and having clear recovery strategies is essential for maintaining system stability and development velocity.

## Common Failure Patterns

### Pattern 1: Instruction or Invariant Violation

**What Happens**: AI agent ignores explicit instructions or violates documented architectural invariants.

**Symptoms**:
- Code that contradicts stated requirements
- Violations of documented patterns (e.g., direct service instantiation instead of factory pattern)
- Security controls bypassed

**Detection**:
- Code review against invariants checklist
- Automated linting for known anti-patterns
- Reviewer agent cross-checking against invariants document

**Recovery**:
- Revert to last known good state
- Re-prompt with explicit reference to violated invariant
- Add automated check to prevent future violations

---

### Pattern 2: Unintended Code Removal

**What Happens**: AI agent removes necessary code while refactoring or implementing new features.

**Symptoms**:
- Tests fail unexpectedly
- Features stop working
- Missing imports, functions, or classes

**Detection**:
- Comprehensive test coverage catches missing functionality
- Code review specifically checking for deletions
- Diff analysis for unexpected removals

**Recovery**:
- `git diff` to identify removed code
- Cherry-pick or restore removed sections
- Re-prompt with explicit instruction: "Do not remove existing functionality"

---

### Pattern 3: API Hallucination

**What Happens**: AI agent invents non-existent APIs, imports, or function signatures.

**Symptoms**:
- Import errors at runtime
- Type errors for non-existent properties
- Function calls to undefined methods

**Detection**:
- Type checking during build
- Import validation in CI/CD
- Runtime error monitoring

**Recovery**:
- Identify correct API from documentation
- Provide AI agent with accurate API reference
- Add validation to catch hallucinations early

---

### Pattern 4: Insecure Code Generation

**What Happens**: AI agent produces code with security vulnerabilities.

**Common Issues**:
- SQL injection vulnerabilities
- Cross-site scripting (XSS)
- Hardcoded credentials
- Insecure deserialization
- Missing authentication checks

**Detection**:
- Static analysis security testing (SAST)
- Security-focused code review
- Penetration testing
- Dependency vulnerability scanning

**Recovery**:
- Immediate revert if in production
- Security-focused re-prompt with explicit constraints
- Add security tests to prevent regression

---

### Pattern 5: Subtle Logic Errors

**What Happens**: Code passes tests but contains logic errors that cause incorrect behavior in edge cases.

**Symptoms**:
- Tests pass but production behavior is wrong
- Edge cases produce incorrect results
- Data corruption or inconsistency

**Detection**:
- Property-based testing
- Fuzzing
- Production monitoring and alerting
- User feedback

**Recovery**:
- Add test case that exposes the bug
- Fix with explicit edge case handling
- Consider expanding test coverage strategy

---

### Pattern 6: Large Diff with Unclear Intent

**What Happens**: AI agent produces a large change set that's difficult to review and may contain hidden issues.

**Symptoms**:
- Hundreds of lines changed
- Multiple unrelated modifications
- Difficulty understanding what changed and why

**Detection**:
- Diff size monitoring
- Review complexity metrics
- Human reviewer confusion

**Recovery**:
- Break into smaller, focused changes
- Request explanation of each logical change
- Use "safe mode" review process (see below)

---

## Recovery Strategies

### Strategy 1: Revert & Re-prompt

**When to Use**: AI produced incorrect output that can't be easily fixed.

**Process**:
1. `git revert` to clean state
2. Analyze what went wrong
3. Add explicit constraints to prompt
4. Retry with clearer instructions

**Example**:
```
Previous attempt removed error handling.
New prompt: "Implement feature X. CRITICAL: Preserve all existing error handling. Do not remove any try/catch blocks or error checks."
```

---

### Strategy 2: Safe Mode for Large Diffs

**When to Use**: AI produces a large change that requires careful review.

**Process**:
1. Pause before committing
2. Review diff section by section
3. Verify each change against requirements
4. Test incrementally if possible
5. Consider breaking into smaller commits

**Threshold**: Changes over 200 lines should trigger safe mode review.

---

### Strategy 3: Rollback Strategies

**Immediate Rollback** (within minutes):
- `git revert HEAD` for last commit
- `git checkout -- <file>` for specific files

**Recent Rollback** (within hours):
- Find good commit: `git log --oneline`
- Reset: `git reset --hard <commit>`
- Or create revert commit: `git revert <commit>`

**Production Rollback**:
- Use deployment platform's rollback feature
- Never manually patch production
- Post-mortem after stabilization

---

### Strategy 4: Detecting Architectural Drift

**Regular Checks**:
- Compare current code against invariants document
- Run architecture fitness functions
- Reviewer agent periodic audits
- Track drift metrics over time

**Warning Signs**:
- Increasing number of invariant violations
- Growing technical debt
- Test coverage declining
- Coupling metrics increasing

**Correction**:
- Schedule architectural review
- Create refactoring slice
- Update invariants if intentionally changed

---

### Strategy 5: Restart vs. Iterate Decision

**Restart When**:
- Fundamental approach is wrong
- Multiple failed iterations
- AI is stuck in incorrect pattern
- Technical debt is accumulating faster than progress

**Iterate When**:
- Core approach is sound
- Issues are isolated and fixable
- Progress is being made
- Changes are well-understood

**Decision Framework**:
```
If (iterations > 3) AND (same error repeating):
    RESTART with different approach
Else if (changes are incremental improvements):
    CONTINUE iterating
Else if (AI seems confused about requirements):
    RESTART with clearer specification
```

---

# 📡 Observability for AI-Generated Code

When AI generates code, debugging becomes harder because the human orchestrator didn't write it line-by-line. Strong observability practices compensate for reduced familiarity.

## Logging Standards

### Structured Logging

**Requirement**: All logs must be structured (JSON or equivalent) for machine parsing.

**Standard Fields**:
```json
{
  "timestamp": "ISO-8601 format",
  "level": "DEBUG|INFO|WARN|ERROR",
  "service": "service-name",
  "correlationId": "request-trace-id",
  "message": "human-readable description",
  "context": {
    "userId": "if applicable",
    "tenantId": "if multi-tenant",
    "operation": "what-is-happening"
  }
}
```

**Log Levels**:
- **ERROR**: Something failed that needs attention
- **WARN**: Something unexpected but handled
- **INFO**: Significant business events
- **DEBUG**: Detailed flow information (disabled in production)

---

### Correlation IDs

**Purpose**: Trace a request through all services and components.

**Implementation**:
- Generate ID at entry point (API gateway, message queue consumer)
- Pass ID through all function calls
- Include ID in all log entries
- Include ID in error responses for support

**Example Flow**:
```
API Gateway → Service A → Service B → Database
     ↓            ↓            ↓           ↓
   Log 1       Log 2        Log 3       Log 4
   (corr-123)  (corr-123)   (corr-123)  (corr-123)
```

---

## AI-Specific Observability

### Model Metadata Logging

When AI agents are invoked at runtime, capture:

```json
{
  "aiInvocation": {
    "model": "model-identifier",
    "provider": "ai-provider-name",
    "tokenCount": {
      "prompt": 1500,
      "completion": 500,
      "total": 2000
    },
    "latencyMs": 2340,
    "temperature": 0.7,
    "requestId": "provider-request-id"
  }
}
```

**Why This Matters**:
- Debug unexpected AI behavior
- Track costs accurately
- Monitor performance trends
- Audit AI decisions

---

### Generation Traceability

Link AI-generated code to its source:

**At Development Time**:
- Commit messages reference AI session
- PR descriptions note AI involvement
- Code comments for complex AI-generated logic (sparingly)

**At Runtime**:
- Log which AI model generated responses
- Track prompt templates used
- Store generation parameters

**Example Commit Message**:
```
feat: add user notification system

AI-assisted implementation using Builder agent.
Session: claude-session-xyz-123
Slice spec: SLICE_NOTIFICATIONS_001

🤖 Generated with [Claude Code](https://claude.com/claude-code)
```

---

## Debugging AI-Generated Code

### Debugging Workflow

1. **Reproduce**: Create minimal reproduction case
2. **Trace**: Follow correlation ID through logs
3. **Isolate**: Identify which component failed
4. **Understand**: Read the code (even though you didn't write it)
5. **Fix**: Make targeted correction
6. **Verify**: Confirm fix and add regression test
7. **Document**: Update documentation if needed

### When Code Is Unfamiliar

Because AI wrote the code, you may not immediately understand it:

1. **Ask the AI to explain**: "Explain what this function does and why it's implemented this way"
2. **Trace data flow**: Follow inputs through to outputs
3. **Check tests**: Tests document expected behavior
4. **Review slice spec**: Understand original requirements
5. **Add logging**: Temporarily add debug logging to understand flow

### Common Debugging Challenges

| Challenge | Solution |
|-----------|----------|
| Don't understand the algorithm | Ask AI to explain with examples |
| Unexpected behavior | Add logging, trace through step-by-step |
| Performance issue | Profile, then ask AI to optimize specific bottleneck |
| Edge case failure | Add test case, then ask AI to fix |

---

## Observability Infrastructure

### Recommended Components

**Logging**:
- Centralized log aggregation
- Log retention policy (30+ days)
- Search and filter capabilities

**Metrics**:
- Request latency (p50, p90, p99)
- Error rates by endpoint
- AI invocation costs
- Resource utilization

**Tracing**:
- Distributed tracing for request flows
- Service dependency mapping
- Latency breakdown by component

**Alerting**:
- Error rate thresholds
- Latency degradation
- AI cost anomalies
- Security events

### Implementation Notes

Choose tools appropriate for your environment:
- Cloud-native monitoring services
- Open-source observability stacks
- Third-party observability platforms

The specific tooling is a Level 2 (Orchestration Profile) decision.

---

## Development Velocity
- Time from slice spec to production
- Number of slices completed per sprint
- AI agent utilization rate

## Quality Metrics
- Test coverage percentage
- Defect escape rate
- Mean time to resolution

## Security Metrics
- Security incidents
- Compliance violations
- Vulnerability remediation time

## Cost Metrics
- Infrastructure costs
- AI token/API costs
- Development efficiency (cost per feature)

### Cost Red Flags

Cost anomalies often indicate workflow problems, not just budget issues. Watch for these warning signs:

| Red Flag | What It Indicates | Action |
|----------|-------------------|--------|
| **Repeated failed generations on same task** | AI is stuck or requirements are unclear | Stop and rethink approach; clarify requirements |
| **Single slice consuming large fraction of budget** | Task too complex or poorly defined | Decompose into smaller slices; consider redesign |
| **Long autonomous loops without progress** | AI spinning without human guidance | Add checkpoints; require human review earlier |
| **Expensive models used for simple tasks** | Model selection not optimized | Review selection heuristics; use cheaper models for routine work |
| **Sudden spike in token usage** | Possible infinite loop or context explosion | Investigate immediately; may indicate bug |
| **Consistently hitting context limits** | Tasks too large or context bloated | Break into smaller tasks; trim unnecessary context |

### Cost as Operational Health Metric

Token usage isn't just a budget line item—it's a health metric for AI interactions:

**Healthy Patterns**:
- Steady, predictable token usage per slice
- Decreasing tokens per feature as patterns mature
- Higher tokens for complex tasks, lower for routine ones

**Unhealthy Patterns**:
- Erratic token usage with no clear correlation to task complexity
- Increasing tokens per feature over time (context bloat)
- High token usage with low output quality

**Monitoring Recommendations**:
- Track tokens per slice/feature over time
- Alert on usage >2x rolling average
- Review high-cost tasks for process improvements
- Correlate token usage with outcome quality

---

# 🎓 Adoption Guide

*See also: [Adoption Maturity Model](#-adoption-maturity-model) for assessing your current level and planning advancement.*

The phases below correspond roughly to achieving **Maturity Level 1** (Foundational). After completing these phases, use the maturity model to guide your progression to Level 2 and Level 3.

## Phase 1: Foundation (Weeks 1-2)
- Study this framework
- Choose AI agents for your context
- Define your environment boundaries
- Create initial prompt contracts

## Phase 2: Setup (Weeks 3-4)
- Set up development environment
- Implement core patterns (factory, security layer)
- Create first invariants document
- Establish branching strategy

## Phase 3: First Slice (Weeks 5-6)
- Create slice specification
- Implement with AI agents
- Run through full workflow
- Document learnings

## Phase 4: Refinement (Weeks 7-8)
- Iterate on patterns
- Update prompt contracts
- Improve invariants
- Train team members

## Phase 5: Scale (Weeks 9+)
- Increase slice velocity
- Add more patterns to library
- Expand test coverage
- Optimize costs

---

# 👥 Scaling AOSD for Teams and Organizations

AOSD works at any scale, from solo developers to large enterprise teams. However, the practices, roles, and coordination mechanisms evolve as team size increases. This section provides guidance for adapting AOSD to different organizational scales.

---

## Solo Developer (Single Orchestrator)

The simplest AOSD configuration: one human orchestrating one or more AI agents.

### Characteristics

| Aspect | Description |
|--------|-------------|
| **Team Size** | 1 developer |
| **AI Agents** | 1-3 agents (typically Builder, optional Reviewer) |
| **Coordination** | None required—single context holder |
| **Branching** | Simple feature branches |
| **Slice Ownership** | All slices owned by the solo developer |

### Roles and Responsibilities

**The Solo Orchestrator** handles all responsibilities:
- Requirements analysis and slice specification
- AI agent direction and prompt engineering
- Code review (often using AI Reviewer agent)
- Testing and validation
- Deployment and operations
- Documentation maintenance

### Workflow

```
Slice Spec → AI Implementation → Self/AI Review → Test → Deploy
     ↑                                                    |
     └────────────────── Iterate ─────────────────────────┘
```

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
| Over-reliance on memory | Lost context between sessions | Document decisions in ADRs |

---

## Small Teams (2-5 Developers)

Small teams introduce coordination needs while remaining agile.

### Characteristics

| Aspect | Description |
|--------|-------------|
| **Team Size** | 2-5 developers |
| **AI Agents** | Multiple agents, possibly shared or individual |
| **Coordination** | Lightweight, often informal |
| **Branching** | Feature branches with simple PR process |
| **Slice Ownership** | Individual or pair ownership |

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

**Option 1: Individual Ownership**
- Each developer owns distinct slices
- Clear accountability
- Risk: knowledge silos

**Option 2: Pair Ownership**
- Two developers share slice responsibility
- Built-in review partner
- Higher coordination overhead

**Option 3: Rotating Ownership**
- Slices rotate between developers
- Spreads knowledge
- Requires good handoff practices

### Branch Ownership

```
main
  ├── feature/alice-user-auth      (Alice owns)
  ├── feature/bob-payment-flow     (Bob owns)
  └── feature/carol-notifications  (Carol owns)
```

**Guidelines**:
- One owner per branch (avoids conflicts)
- Clear naming conventions
- Short-lived branches (merge within days, not weeks)
- PR review required before merge

### Coordination Patterns

**Daily Sync** (15 minutes):
- What slices are in progress
- Any blockers or conflicts
- Coordination needs for shared components

**Shared Context**:
- Common prompt templates
- Shared invariants document
- Team-accessible AI configuration

**Async Coordination**:
- Work item comments for decisions
- PR descriptions for context
- Shared documentation updates

### AI-Assisted Coordination

| Mechanism | Description |
|-----------|-------------|
| **Shared Prompt Library** | Common prompts ensure consistent AI behavior across team |
| **Invariants Enforcement** | AI agents check against shared invariants |
| **Cross-Review** | Reviewer agent can check for conflicts with other in-progress work |
| **Documentation Generation** | AI maintains shared understanding through generated docs |

---

## Medium Teams (6-15 Developers)

Medium teams require more formal coordination and may split into sub-teams.

### Characteristics

| Aspect | Description |
|--------|-------------|
| **Team Size** | 6-15 developers |
| **AI Agents** | Team-level and individual agents |
| **Coordination** | Formal processes, regular ceremonies |
| **Branching** | Feature branches with integration branches |
| **Slice Ownership** | Sub-team or domain ownership |

### Organizational Structure

**Option 1: Domain Teams**
```
Team
├── Frontend Sub-team (3-4 devs)
│   └── UI slices, client-side logic
├── Backend Sub-team (3-4 devs)
│   └── API slices, business logic
└── Platform Sub-team (2-3 devs)
    └── Infrastructure, shared services
```

**Option 2: Feature Teams**
```
Team
├── Feature Team A (3-4 devs)
│   └── Complete vertical slices for Feature A
├── Feature Team B (3-4 devs)
│   └── Complete vertical slices for Feature B
└── Platform Team (2-3 devs)
    └── Shared infrastructure and tooling
```

### Roles and Responsibilities

**Tech Lead / Principal Orchestrator**:
- Maintains overall architectural vision
- Reviews cross-team changes
- Manages team-level AI configuration
- Resolves conflicts between sub-teams
- Ensures invariants are followed

**Sub-team Leads**:
- Manage sub-team slice allocation
- Conduct primary code reviews
- Maintain domain-specific patterns
- Coordinate with other sub-teams

**Team Members**:
- Implement assigned slices
- Participate in reviews
- Follow established patterns
- Contribute to shared documentation

### Branch Strategy

```
main
├── integration/sprint-42           (Integration branch)
│   ├── feature/frontend-new-dashboard
│   ├── feature/backend-analytics-api
│   └── feature/platform-caching-layer
└── integration/sprint-43           (Next sprint)
    └── ...
```

**Integration Branches**:
- Collect related features before merging to main
- Allow testing of feature interactions
- Reduce main branch churn
- Enable coordinated releases

### Slice Distribution

**Domain-Based Distribution**:
- Slices assigned based on domain expertise
- Clear ownership boundaries
- Reduced coordination overhead within domains
- Requires cross-domain coordination for vertical slices

**Capacity-Based Distribution**:
- Slices assigned based on availability
- Better load balancing
- Requires more context switching
- Good for teams with generalist skills

### Coordination Ceremonies

| Ceremony | Frequency | Purpose |
|----------|-----------|---------|
| **Daily Standup** | Daily | Status, blockers, coordination needs |
| **Slice Planning** | Weekly | Allocate and prioritize slices |
| **Architecture Review** | Bi-weekly | Cross-cutting decisions, invariant updates |
| **Integration Testing** | Per sprint | Validate feature interactions |
| **Retrospective** | Per sprint | Process improvement |

### AI-Assisted Coordination

**Team-Level AI Configuration**:
- Shared prompt templates managed centrally
- Invariants document versioned and distributed
- AI agent access controls (who can use which models)
- Cost tracking per sub-team

**Cross-Team AI Review**:
- Reviewer agent checks for conflicts with other sub-teams' work
- Automated detection of pattern violations
- AI-generated integration test suggestions

**Documentation Automation**:
- AI generates cross-team dependency documentation
- Automatic API compatibility reports
- Generated release notes from slice completions

---

## Large Enterprise (15+ Developers, Multiple Teams)

Enterprise scale requires formal governance, tooling, and coordination infrastructure.

### Characteristics

| Aspect | Description |
|--------|-------------|
| **Team Size** | 15+ developers across multiple teams |
| **AI Agents** | Centrally managed with team-level customization |
| **Coordination** | Formal governance, dedicated roles |
| **Branching** | Sophisticated strategy with release branches |
| **Slice Ownership** | Team-level with clear interfaces |

### Organizational Structure

```
Organization
├── Product Team A (10-15 devs)
│   ├── Sub-team A1
│   └── Sub-team A2
├── Product Team B (10-15 devs)
│   ├── Sub-team B1
│   └── Sub-team B2
├── Platform Team (5-10 devs)
│   └── Shared infrastructure, AI tooling
└── Architecture Team (2-4 devs)
    └── Standards, governance, cross-team coordination
```

### Roles and Responsibilities

**Chief Architect / AOSD Program Lead**:
- Defines organizational AOSD standards
- Manages framework adoption and evolution
- Oversees AI governance and security
- Coordinates cross-product architecture

**Product Architects**:
- Maintain product-level architectural vision
- Ensure alignment with organizational standards
- Manage product-level invariants
- Coordinate with other product teams

**Platform Team**:
- Provides shared AI infrastructure
- Manages prompt template libraries
- Maintains testing and deployment tooling
- Monitors AI costs and usage

**Team Leads** (as in Medium Teams):
- Manage team slice allocation
- Ensure pattern compliance
- Conduct reviews and mentoring

### Branch Strategy

```
main
├── release/v2.1                    (Release branch)
├── release/v2.2                    (Next release)
├── integration/team-a-q4           (Team integration)
│   ├── feature/a1-user-management
│   └── feature/a2-reporting
├── integration/team-b-q4
│   └── feature/b1-payment-processing
└── hotfix/v2.0.1                   (Production fix)
```

**Release Branches**:
- Long-lived branches for release stabilization
- Hotfixes applied to release branches
- Clear promotion path to production

**Team Integration Branches**:
- Isolate team work before cross-team integration
- Enable team-level testing
- Reduce integration conflicts

### Slice Distribution at Scale

**Hierarchical Distribution**:
```
Organizational Backlog
    ↓ (Product assignment)
Product Backlogs
    ↓ (Team assignment)
Team Backlogs
    ↓ (Individual assignment)
Developer Work
```

**Interface Contracts**:
- Teams define API contracts before implementation
- Contracts reviewed and approved cross-team
- AI agents validate implementations against contracts
- Contract changes require formal approval

### Governance Framework

**AI Governance**:
- Approved AI models and providers
- Data handling and privacy requirements
- Cost budgets and controls
- Security and compliance reviews

**Architecture Governance**:
- Invariants managed at multiple levels (org, product, team)
- Architecture Decision Records (ADRs) for significant decisions
- Regular architecture review boards
- Pattern libraries with ownership

**Change Governance**:
- Cross-team changes require additional review
- Breaking changes require migration plans
- Deployment windows and change freezes
- Incident response procedures

### AI-Assisted Coordination at Scale

**Centralized AI Platform**:
- Managed AI service access
- Prompt template repositories
- Usage monitoring and cost allocation
- Security and compliance controls

**Cross-Team AI Coordination**:
- AI-detected dependency conflicts
- Automated compatibility checking
- Generated integration documentation
- Cross-team impact analysis

**AI-Powered Metrics**:
- Slice velocity by team
- AI cost per team/product
- Quality metrics correlation
- Predictive bottleneck identification

### Scaling Challenges and Solutions

| Challenge | Solution |
|-----------|----------|
| **Context Fragmentation** | Shared documentation, AI-maintained knowledge bases |
| **Inconsistent Patterns** | Centralized pattern library, automated enforcement |
| **Coordination Overhead** | Clear interfaces, async coordination, AI assistance |
| **Cost Management** | Team budgets, usage monitoring, optimization reviews |
| **Knowledge Silos** | Rotation programs, cross-team reviews, documentation |
| **AI Governance** | Central platform team, clear policies, auditing |

---

## Transition Patterns

### Growing from Solo to Small Team

**When to Transition**:
- Consistent backlog exceeds individual capacity
- Need for specialized skills
- Risk mitigation through redundancy

**Key Changes**:
- Establish shared documentation practices
- Define minimal coordination ceremonies
- Create shared prompt templates
- Implement PR review process

### Growing from Small to Medium Team

**When to Transition**:
- Coordination overhead increasing
- Distinct domains emerging
- Need for more formal processes

**Key Changes**:
- Create sub-team structure
- Implement integration branches
- Establish formal review ceremonies
- Define domain boundaries and interfaces

### Growing from Medium to Enterprise

**When to Transition**:
- Multiple products or significant complexity
- Need for organizational standards
- Compliance or governance requirements

**Key Changes**:
- Establish architecture team
- Create AI platform team
- Implement formal governance
- Define cross-team coordination infrastructure

---

## Summary: Scaling Dimensions

| Dimension | Solo | Small (2-5) | Medium (6-15) | Enterprise (15+) |
|-----------|------|-------------|---------------|------------------|
| **Coordination** | None | Informal | Formal ceremonies | Governance framework |
| **Branching** | Simple | Feature PRs | Integration branches | Release branches |
| **Slice Ownership** | Individual | Individual/Pair | Sub-team | Team + Interfaces |
| **AI Management** | Individual | Shared templates | Team configuration | Central platform |
| **Documentation** | Personal discipline | Shared practices | Formal standards | Managed knowledge base |
| **Review Process** | AI Reviewer | Peer + AI | Multi-level | Architectural review |

---

# 🔄 Framework Evolution

## Living Document

This framework evolves through:
- Real-world implementation experience
- Feedback from orchestrators
- Advances in AI capabilities
- New security requirements
- Platform changes

## Update Process

1. **Identify Need**: Document gaps or issues
2. **Propose Change**: Create detailed proposal
3. **Review**: Evaluate impact on existing implementations
4. **Update**: Modify framework documentation
5. **Communicate**: Notify all AOSD practitioners
6. **Validate**: Test changes in real projects

---

# 📦 Versioning & Compatibility

## Semantic Versioning

AOSD Framework follows [Semantic Versioning](https://semver.org/) (MAJOR.MINOR.PATCH):

- **MAJOR**: Breaking changes that require updates to existing Orchestration Profiles
- **MINOR**: New features, principles, or patterns that are backward-compatible
- **PATCH**: Bug fixes, clarifications, and documentation improvements

## What Constitutes a Breaking Change

Changes that increment the MAJOR version:
- Removal or fundamental redefinition of core principles
- Changes to the three-level architecture model
- Removal of required sections from templates
- Changes that would invalidate existing Level 2 Orchestration Profiles

## What Constitutes a Feature

Changes that increment the MINOR version:
- New principles added to the framework
- New architecture patterns added
- New templates or training modules
- Enhanced guidance that extends existing concepts

## What Constitutes a Patch

Changes that increment the PATCH version:
- Typo corrections and grammatical fixes
- Clarifications that don't change meaning
- Example updates and improved documentation
- Minor wording improvements

## Compatibility Declaration

Orchestration Profiles (Level 2) should declare framework compatibility:

```markdown
## Framework Compatibility
**AOSD Framework Version**: 0.x.x
**Minimum Compatible**: 0.1.0
**Tested With**: [specific version]
```

**Compatibility Rules**:
- Profiles targeting version 0.x should work with any 0.y where y ≥ x
- Major version changes may require profile updates
- Check CHANGELOG.md for migration guidance when upgrading

## Version Tracking

- Current version is declared in the document header
- All changes are documented in CHANGELOG.md
- Releases are tagged in version control

---

# 🔮 Future-Proofing AOSD

AI technology evolves rapidly. This section provides guidance for keeping AOSD relevant and effective as capabilities, tools, and best practices change.

## Adopting New AI Tools

When new AI tools, models, or capabilities emerge, use this evaluation and integration process.

### Evaluation Framework

Before adopting any new AI tool, assess it against these criteria:

| Criterion | Questions to Answer |
|-----------|---------------------|
| **Capability Fit** | Does this tool address a gap in current workflows? What can it do that existing tools cannot? |
| **AOSD Alignment** | Does the tool support AOSD principles (invariants, slices, human-in-the-loop)? Can it respect architectural constraints? |
| **Integration Path** | How will it integrate with existing agents and workflows? What changes are required? |
| **Security Posture** | Does it meet security requirements? How does it handle sensitive data? |
| **Cost-Benefit** | What's the total cost (licensing, training, integration)? What's the expected productivity gain? |
| **Reversibility** | Can we roll back if it doesn't work out? What's the exit strategy? |

### Adoption Process

```
Phase 1: Discovery (1-2 weeks)
├── Identify tool capabilities
├── Map to AOSD principles
├── Assess security implications
└── Document initial evaluation

Phase 2: Pilot (2-4 weeks)
├── Select low-risk slice for trial
├── Define success metrics
├── Run controlled experiment
└── Gather feedback

Phase 3: Integration (2-4 weeks)
├── Update Orchestration Profile
├── Create/update prompt templates
├── Train team members
└── Document patterns and anti-patterns

Phase 4: Rollout (ongoing)
├── Gradual expansion to more slices
├── Monitor metrics
├── Refine practices
└── Share learnings
```

### Tool Categories and Considerations

| Tool Category | AOSD Considerations |
|---------------|---------------------|
| **New LLM Models** | Update MODEL_ROLES.md assignments; test against existing prompts; validate invariant compliance |
| **Code Generation Tools** | Integrate with builder agent workflow; ensure output meets coding standards; validate security |
| **Review/Analysis Tools** | Align with reviewer agent responsibilities; establish trust levels; define escalation paths |
| **Specialized Agents** | Define clear role boundaries; establish handoff protocols; document capabilities |
| **Infrastructure/DevOps AI** | Integrate with environment factory patterns; maintain security boundaries; audit access |

### Integration Patterns

**Pattern 1: Tool Wrapper**
```
Existing Workflow → Tool Wrapper → New Tool
                         ↓
                  Validation Layer
                         ↓
                  AOSD Compliance Check
```

Wrap new tools in an adapter that validates outputs against AOSD invariants before accepting them.

**Pattern 2: Parallel Evaluation**
```
Task → Existing Tool → Compare → Select Best
    → New Tool     ─────┘
```

Run new and existing tools in parallel during pilot phase to compare quality and reliability.

**Pattern 3: Graduated Trust**
```
Level 1: All outputs require human review
Level 2: Low-risk outputs auto-accepted, others reviewed
Level 3: Most outputs auto-accepted, high-risk reviewed
Level 4: Full trust with audit logging
```

Increase tool autonomy as confidence builds through successful use.

---

## Expanding Roles and Agents

As AI capabilities grow, you may need to add new agent roles or expand existing ones.

### When to Add New Agents

Consider adding a new agent role when:
- A specialized task type becomes common enough to warrant dedicated tooling
- Existing agents are overloaded with diverse responsibilities
- New AI capabilities enable previously impossible automation
- Quality would improve with specialized focus

**Anti-pattern**: Adding agents for novelty rather than need. Each agent adds coordination overhead.

### New Agent Definition Template

When adding a new agent role, document:

```markdown
## [Agent Name] Agent

### Purpose
[One sentence describing this agent's unique value]

### Responsibilities
- [Specific task 1]
- [Specific task 2]
- [Specific task 3]

### Capabilities Required
- [Technical capability 1]
- [Technical capability 2]

### Inputs
- [What this agent needs to do its job]

### Outputs
- [What this agent produces]

### Boundaries
- DOES: [explicit inclusions]
- DOES NOT: [explicit exclusions]

### Handoff Protocols
- Receives work from: [other agents/human]
- Passes work to: [other agents/human]
- Escalates to: [human/senior agent]

### Trust Level
- [Low/Medium/High] - [justification]

### Metrics
- [How to measure this agent's effectiveness]
```

### Evolving Existing Agent Roles

As models improve, existing agents may gain new capabilities:

| Evolution Type | Process |
|----------------|---------|
| **Capability Expansion** | Update role documentation; expand prompt templates; adjust trust levels; monitor quality |
| **Responsibility Shift** | Redistribute tasks between agents; update handoff protocols; retrain team |
| **Autonomy Increase** | Reduce human checkpoints gradually; maintain audit logging; watch for quality degradation |

### Agent Coordination Patterns

As agent count grows, coordination becomes critical:

**Hub and Spoke**
```
           Builder
              ↑
Reviewer ← Orchestrator → Specialist
              ↓
           Analyst
```
Central orchestrator coordinates all agents.

**Pipeline**
```
Spec → Planner → Builder → Reviewer → Deployer
```
Agents work in sequence with clear handoffs.

**Collaborative**
```
Builder ←→ Reviewer
   ↕          ↕
Specialist ←→ Analyst
```
Agents communicate directly based on need.

Choose the pattern that matches your workflow complexity and team structure.

---

## Evolving Training Modules

AOSD training (SYLLABUS) should evolve as the framework evolves.

### Training Evolution Triggers

Update training materials when:
- Framework version increments (new principles, patterns)
- New tools are adopted (new workflows to teach)
- Common mistakes emerge (new anti-patterns to cover)
- Team feedback indicates gaps (confusion points)
- AI capabilities change (new possibilities or constraints)

### Self-Updating Curriculum Patterns

**Pattern 1: Feedback-Driven Updates**
```
Training Delivery → Practice → Feedback Collection → Analysis → Update
        ↑                                                        |
        └────────────────────────────────────────────────────────┘
```

Collect feedback after each training module and use it to improve content.

**Pattern 2: Usage-Based Refinement**
```
Monitor common errors in production
        ↓
Identify knowledge gaps
        ↓
Create targeted training content
        ↓
Integrate into curriculum
```

Use real-world mistakes to drive training improvements.

**Pattern 3: AI-Assisted Content Generation**
```
New framework feature added
        ↓
AI generates draft training content
        ↓
Human reviews and refines
        ↓
Integrate into SYLLABUS
```

Use AI to accelerate training content creation while maintaining human oversight.

### Training Module Lifecycle

```
Status Progression:
DRAFT → REVIEW → ACTIVE → DEPRECATED → ARCHIVED

DRAFT:      New content being developed
REVIEW:     Content under evaluation
ACTIVE:     Current, recommended training
DEPRECATED: Outdated, but still available
ARCHIVED:   Historical reference only
```

### Keeping Training Aligned with Framework

| Framework Change | Training Action |
|------------------|-----------------|
| New principle added | Create new module or update existing |
| Pattern modified | Update relevant exercises and examples |
| Anti-pattern discovered | Add to troubleshooting training |
| Tool deprecated | Mark training as deprecated |
| Version bump | Review all modules for accuracy |

### Training Effectiveness Metrics

Track these to ensure training remains valuable:

| Metric | Target | Action if Below Target |
|--------|--------|------------------------|
| Completion rate | >80% | Simplify or break into smaller modules |
| Assessment pass rate | >70% | Clarify content or add examples |
| Time to first successful slice | <1 week | Add hands-on exercises |
| Post-training error rate | <10% | Identify and address gaps |
| Practitioner satisfaction | >4/5 | Gather and act on feedback |

---

## Living Methodology Principles

AOSD is designed to evolve. These principles guide continuous improvement.

### Core Evolution Principles

1. **Backwards Compatibility First**
   - New features should not break existing implementations
   - Deprecation before removal
   - Clear migration paths for breaking changes

2. **Evidence-Based Changes**
   - Changes should be driven by real-world experience
   - Document the problem before proposing solutions
   - Validate changes through pilot implementations

3. **Community Input**
   - Practitioners inform framework evolution
   - Feedback loops at all levels
   - Diverse perspectives improve the framework

4. **Principled Extension**
   - New patterns must align with existing principles
   - If a pattern contradicts principles, reconsider the pattern
   - Exceptions require explicit justification

5. **Sustainable Pace**
   - Don't change too much too fast
   - Allow time for adoption between major changes
   - Balance innovation with stability

### Continuous Improvement Process

```
         ┌──────────────────────────────────────────────┐
         ↓                                              |
    Experience → Observe → Analyze → Propose → Test → Adopt
         |                                              ↑
         └──────────────────────────────────────────────┘
```

**Experience**: Use AOSD in real projects
**Observe**: Notice friction, gaps, or opportunities
**Analyze**: Understand root causes and patterns
**Propose**: Draft specific improvements
**Test**: Pilot changes in controlled settings
**Adopt**: Roll out successful changes broadly

### Framework Health Indicators

Monitor these to assess AOSD effectiveness:

| Indicator | Healthy Range | Warning Sign |
|-----------|---------------|--------------|
| Adoption rate | Growing or stable | Declining adoption |
| Practitioner satisfaction | >4/5 | <3/5 average rating |
| Time to productivity | Decreasing | Increasing onboarding time |
| Common error patterns | Decreasing | Same errors recurring |
| Framework version currency | <2 versions behind | >2 versions behind |

### Innovation Sandbox

Test new ideas without destabilizing the core framework:

```
Core Framework (Stable)
    ↓
Extension Points (Controlled)
    ↓
Experimental Features (Sandbox)
    ↓
Community Experiments (External)
```

- **Core**: Battle-tested principles and patterns
- **Extensions**: Documented, optional additions
- **Experimental**: Clearly marked, may change
- **Community**: External contributions, not yet validated

---

## Version Migration Strategies

When AOSD major versions change, use these strategies to migrate.

### Migration Planning

Before migrating to a new major version:

1. **Review CHANGELOG** for all changes since your current version
2. **Identify breaking changes** that affect your Orchestration Profile
3. **Assess impact** on each Level 3 application
4. **Plan migration order** (usually: Framework → Profile → Applications)
5. **Estimate effort** for each migration step
6. **Schedule migration windows** during low-risk periods

### Migration Approaches

**Approach 1: Big Bang**
```
Migrate everything at once

Pros: Single migration effort, clean cutover
Cons: High risk, significant coordination required
Best for: Small teams, simple implementations
```

**Approach 2: Gradual Migration**
```
Migrate incrementally over time

Pros: Lower risk, learn as you go
Cons: Longer transition, compatibility management
Best for: Large teams, complex implementations
```

**Approach 3: Parallel Running**
```
Run old and new versions simultaneously

Pros: Easy rollback, thorough validation
Cons: Double maintenance, potential confusion
Best for: Critical systems, risk-averse organizations
```

### Migration Checklist

```markdown
## Pre-Migration
- [ ] Read release notes and CHANGELOG
- [ ] Back up current Orchestration Profile
- [ ] Document current state of all Level 3 applications
- [ ] Identify all breaking changes affecting your implementation
- [ ] Create migration plan with timeline
- [ ] Communicate plan to all stakeholders

## Migration Execution
- [ ] Update Orchestration Profile to new version
- [ ] Update invariants documents
- [ ] Update prompt templates
- [ ] Update each Level 3 application's AOSD folder
- [ ] Run validation tests
- [ ] Update training materials

## Post-Migration
- [ ] Verify all applications function correctly
- [ ] Update version declarations
- [ ] Document any issues encountered
- [ ] Update team on new features/changes
- [ ] Archive old version documentation
```

### Handling Breaking Changes

| Change Type | Migration Strategy |
|-------------|-------------------|
| Principle renamed | Search and replace in all documents |
| Principle removed | Assess if you relied on it; find alternative |
| Template changed | Update all documents using that template |
| Pattern deprecated | Plan transition to replacement pattern |
| New required section | Add to all relevant documents |

### Version Compatibility Matrix

Maintain a compatibility matrix for your implementation:

```markdown
| Application | Framework Version | Profile Version | Status |
|-------------|-------------------|-----------------|--------|
| App A       | 1.10.x            | 2.3.x           | Current |
| App B       | 1.10.x            | 2.3.x           | Current |
| App C       | 1.9.x             | 2.2.x           | Needs upgrade |
| Legacy App  | 1.5.x             | 1.8.x           | Deprecated |
```

---

## Contribution Guidelines

For those contributing to AOSD evolution.

### Types of Contributions

| Contribution Type | Description | Process |
|-------------------|-------------|---------|
| **Bug Reports** | Errors or inconsistencies in documentation | Open issue with details |
| **Clarifications** | Requests for clearer explanations | Open issue or submit PR |
| **New Patterns** | Proven patterns from real implementations | Propose with evidence |
| **Tool Integrations** | Integration guides for specific tools | Submit as example |
| **Training Content** | Additional learning materials | Submit for review |

### Contribution Process

```
1. Check Existing Issues
   ↓
2. Open Discussion Issue
   ↓
3. Gather Feedback
   ↓
4. Submit Proposal/PR
   ↓
5. Review Process
   ↓
6. Integration
```

### Quality Standards

All contributions should:
- Align with existing AOSD principles
- Be platform/tool-agnostic (for core framework)
- Include practical examples
- Be clearly written for the target audience
- Not contradict existing guidance without justification

### Contribution Checklist

```markdown
## Before Submitting
- [ ] Searched existing issues/PRs for duplicates
- [ ] Aligned with AOSD principles
- [ ] Tested in real-world scenario (if applicable)
- [ ] Written clearly and concisely
- [ ] Included examples where helpful
- [ ] Updated relevant cross-references

## Submission
- [ ] Clear title describing the change
- [ ] Detailed description of what and why
- [ ] Link to related issues
- [ ] List of files changed

## After Submission
- [ ] Respond to feedback promptly
- [ ] Make requested changes
- [ ] Update PR description if scope changes
```

### Recognition

Contributors who improve AOSD are acknowledged:
- Significant contributions noted in CHANGELOG
- Major contributors listed in documentation
- Community contributors welcomed and thanked

---

# 📚 Related Documents

- **IMPLEMENTATION_TEMPLATE.md**: Guide for documenting your environment-specific choices
- **ANTI_PATTERNS.md**: Comprehensive guide to patterns that violate AOSD principles and must be avoided
- **MULTI_AGENT_PATTERNS.md**: Safe patterns for coordinating multiple AI agents with safety guardrails
- **PARALLEL_DEVELOPMENT.md**: Patterns for running multiple AI agent sessions simultaneously
- **INVARIANTS_COOKBOOK.md**: Executable fitness functions for enforcing architectural invariants
- **TROUBLESHOOTING.md**: Comprehensive troubleshooting for AI agent issues, integrated AI features, agent drift, restrictive environments, and team workflows
- **MODEL_ROLES.md**: AI agent role definitions
- **examples/reference-implementations/**: Reference implementations for various platforms

---

# 🏁 Conclusion

AOSD Framework provides the foundational principles for AI-orchestrated development while remaining agnostic to specific tools and platforms.

**Key Takeaways**:
- Multi-agent orchestration requires clear roles and boundaries
- Architectural invariants prevent AI drift
- Environment isolation ensures security
- Slice-based development enables incremental delivery
- Testing strategy balances speed and coverage
- Human oversight maintains strategic control
- Work item lifecycle ensures traceability and accountability

**Next Steps**:
1. Read IMPLEMENTATION_TEMPLATE.md to document your choices
2. Review examples/aws-implementation/ for reference
3. Create your first slice specification
4. Begin orchestrating AI agents in your environment

---

**End of AOSD_FRAMEWORK.md**
