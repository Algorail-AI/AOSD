# AOSD Architecture Patterns

**Purpose**: This document defines reusable architecture patterns for AOSD implementations, providing solutions to common problems in AI-orchestrated systems development.

**Part of**: [AOSD Framework](./AOSD_FRAMEWORK.md)

*This section was extracted from AOSD_FRAMEWORK.md on 2024-12 to keep the framework AI-consumable.*

---

## Pattern 1: Environment Factory

**Problem**: Direct instantiation of cloud service clients leads to inconsistent configuration and difficult testing.

**Solution**: Use a factory pattern that:
- Centralizes service client creation
- Manages environment-specific configuration
- Enables test isolation via dependency injection
- Prevents credential and region drift

**Implementation Notes**:
- Create factory for your cloud platform (AWS, Azure, GCP, etc.)
- Use environment variables for configuration
- Provide test doubles/mocks through the same interface

---

## Pattern 2: API Security Layer

**Problem**: APIs need consistent security controls across all endpoints.

**Solution**: Implement a security layer that:
- Enforces authentication and authorization
- Provides rate limiting and IP filtering
- Protects against common web attacks
- Operates in enforcement mode in production

**Implementation Notes**:
- Use your platform's web application firewall or equivalent
- Define security policies as infrastructure code
- Test security controls automatically
- Never disable security in production

---

## Pattern 3: Multi-Tenant Data Isolation

**Problem**: Shared infrastructure must prevent data leakage between tenants.

**Solution**: Design data models with:
- Explicit tenant identifiers in keys/indexes
- Tenant validation at request boundaries
- Prohibition on cross-tenant queries
- Audit logging of all data access

**Implementation Notes**:
- Choose appropriate multi-tenancy model (database-per-tenant, schema-per-tenant, row-level security)
- Validate tenant ID on every request
- Use partition keys or indexes that include tenant ID
- Test cross-tenant isolation explicitly

---

## Pattern 4: Deployment Bundling

**Problem**: Serverless functions require specific bundling to avoid import failures.

**Solution**: Standardize bundling process:
- Include dependencies in deployment package
- Maintain correct directory structure
- Copy handler files to expected locations
- Use deterministic builds

**Implementation Notes**:
- Document bundling structure for your platform
- Create bundling scripts or configuration
- Test bundled artifacts before deployment
- Use content hashing to detect changes

---

## Pattern 5: Decision Catalog

**Problem**: Cross-cutting decisions need to be normalized and validated across the system.

**Solution**: Create a typed decision catalog that:
- Defines allowed decision keys and value types
- Normalizes and validates inputs
- Handles synonyms and variations
- Maintains decision precedence (user > AI > baseline)

**Implementation Notes**:
- Define schema for decision types
- Implement validation rules
- Create workflow for proposing/approving decisions
- Integrate with AI prompt generation

### Decision Schema

Every decision in the catalog has a defined structure:

```json
{
  "key": "document.format",
  "value": "markdown",
  "valueType": "enum",
  "allowedValues": ["markdown", "html", "pdf", "docx"],
  "source": "user",
  "precedence": 1,
  "timestamp": "2025-01-15T10:30:00Z",
  "context": "user-preferences",
  "description": "Output format for generated documents",
  "validUntil": null
}
```

**Schema Fields**:

| Field | Type | Description |
|-------|------|-------------|
| `key` | string | Unique identifier using dot notation (e.g., `document.format`) |
| `value` | any | The decision value (type depends on `valueType`) |
| `valueType` | enum | Type of value: `string`, `number`, `boolean`, `enum`, `array` |
| `allowedValues` | array | For enums: list of valid options |
| `source` | enum | Who made this decision: `user`, `ai`, `baseline`, `system` |
| `precedence` | number | Priority level (lower = higher priority) |
| `timestamp` | ISO-8601 | When the decision was made |
| `context` | string | Where this decision applies |
| `description` | string | Human-readable explanation |
| `validUntil` | ISO-8601 | Optional expiration |

### Precedence Rules

Decisions from different sources can conflict. Precedence determines which wins:

```
user (precedence: 1) > ai (precedence: 2) > baseline (precedence: 3) > system (precedence: 4)
```

**How Precedence Works**:

| Scenario | User Says | AI Says | Baseline | Result |
|----------|-----------|---------|----------|--------|
| User overrides AI | "formal" | "casual" | "neutral" | "formal" |
| AI overrides baseline | - | "casual" | "neutral" | "casual" |
| Baseline applies | - | - | "neutral" | "neutral" |
| User clears preference | null | "casual" | "neutral" | "casual" |

**Resolution Algorithm**:
```python
def resolve_decision(key: str, catalog: DecisionCatalog) -> any:
    """Return the highest-precedence non-null value for a decision."""
    decisions = catalog.get_all(key)  # All decisions for this key
    sorted_decisions = sorted(decisions, key=lambda d: d.precedence)

    for decision in sorted_decisions:
        if decision.value is not None:
            return decision.value

    return None  # No decision made
```

### Versioning Decisions

Decisions evolve over time. The catalog tracks history:

```json
{
  "key": "document.tone",
  "history": [
    {
      "value": "formal",
      "source": "baseline",
      "timestamp": "2025-01-01T00:00:00Z",
      "reason": "Initial default"
    },
    {
      "value": "professional",
      "source": "ai",
      "timestamp": "2025-01-10T14:00:00Z",
      "reason": "AI detected business context"
    },
    {
      "value": "friendly",
      "source": "user",
      "timestamp": "2025-01-15T10:30:00Z",
      "reason": "User preference for customer communications"
    }
  ],
  "current": {
    "value": "friendly",
    "source": "user",
    "effectiveFrom": "2025-01-15T10:30:00Z"
  }
}
```

**Why Version Decisions**:
- Audit trail for compliance
- Roll back problematic changes
- Understand how preferences evolved
- Debug unexpected AI behavior

### Integration with AI Prompts

Decisions inject into AI prompts automatically:

```python
def build_prompt(task: str, catalog: DecisionCatalog) -> str:
    """Build AI prompt with relevant decisions injected."""
    decisions = catalog.get_context_decisions("document-generation")

    decision_block = "## Active Decisions\n"
    for key, value in decisions.items():
        decision_block += f"- {key}: {value}\n"

    return f"""
{decision_block}

## Task
{task}

Apply the above decisions when generating output.
"""
```

**Example Injected Prompt**:
```
## Active Decisions
- document.format: markdown
- document.tone: friendly
- document.length: concise
- document.include_examples: true

## Task
Generate a user guide for the login feature.

Apply the above decisions when generating output.
```

### Worked Example: Document Generation Preferences

**Scenario**: A team uses AI to generate various documents. They need consistent preferences.

**Baseline Decisions** (defaults):
```json
[
  {"key": "doc.format", "value": "markdown", "source": "baseline"},
  {"key": "doc.tone", "value": "professional", "source": "baseline"},
  {"key": "doc.length", "value": "standard", "source": "baseline"},
  {"key": "doc.audience", "value": "technical", "source": "baseline"}
]
```

**AI-Detected Decisions** (context-aware):
```json
[
  {"key": "doc.tone", "value": "formal", "source": "ai",
   "reason": "Detected legal/compliance context"},
  {"key": "doc.audience", "value": "executive", "source": "ai",
   "reason": "Document mentions board presentation"}
]
```

**User Decisions** (explicit overrides):
```json
[
  {"key": "doc.length", "value": "brief", "source": "user",
   "reason": "User requested summary format"}
]
```

**Resolved Decisions**:
| Key | Resolved Value | Source | Reason |
|-----|----------------|--------|--------|
| doc.format | markdown | baseline | No override |
| doc.tone | formal | ai | AI detected legal context |
| doc.length | brief | user | User override |
| doc.audience | executive | ai | AI detected exec context |

### Common Anti-Patterns

**Anti-Pattern 1: Hardcoded Decisions**
```python
# BAD: Decision buried in code
def generate_report():
    return ai.generate(format="pdf", tone="formal")  # Hardcoded!
```

```python
# GOOD: Pull from catalog
def generate_report(catalog):
    format = catalog.resolve("report.format")
    tone = catalog.resolve("report.tone")
    return ai.generate(format=format, tone=tone)
```

**Anti-Pattern 2: No Conflict Resolution**
```python
# BAD: Last write wins, unpredictable
decisions["tone"] = "casual"      # AI sets
decisions["tone"] = "formal"      # User sets
decisions["tone"] = "neutral"     # Baseline overwrites!
```

```python
# GOOD: Precedence-aware resolution
catalog.set("tone", "casual", source="ai")
catalog.set("tone", "formal", source="user")
catalog.set("tone", "neutral", source="baseline")
# resolve() returns "formal" (user precedence)
```

**Anti-Pattern 3: Missing Validation**
```python
# BAD: Accept any value
catalog.set("format", "pptx")  # Not a valid option!
```

```python
# GOOD: Validate against schema
catalog.set("format", "pptx")
# Raises: ValueError("'pptx' not in allowed values: ['markdown', 'html', 'pdf']")
```

---

## Pattern 6: Revision Instructions

**Problem**: Users need to guide AI agents without re-explaining context every time.

**Solution**: Persist user guidance as revision instructions:
- Store instructions with the work product
- Inject into AI prompts automatically
- Version instructions over time
- Allow updates without losing history

**Implementation Notes**:
- Define storage mechanism for instructions
- Create prompt templates that include instructions
- Track instruction effectiveness
- Provide UI for managing instructions

---

## Pattern 7: Agent Context Memory Files

**Problem**: AI agents lose context between sessions, requiring humans to re-explain project history, decisions, and learnings each time.

**Solution**: Persist AI agent context in a standardized file structure:
- Store session context, decisions, and learnings in version-controlled files
- Provide consistent context to all agents working on the project
- Enable smooth handoffs between agent sessions
- Allow agents to build cumulative knowledge about the codebase

**Folder Structure**:
```
.ai/
├── memory/
│   ├── session/           # Current session context (often ephemeral)
│   ├── decisions/         # Key decisions made during development
│   ├── learnings/         # What AI learned about codebase patterns
│   └── handoffs/          # Context for agent transitions
├── context/
│   └── project-summary.md # High-level project understanding
└── .gitignore             # Exclude sensitive/ephemeral files
```

**Key Principles**:
- **Tool-Agnostic**: Works with Claude Code, ChatGPT, Cursor, Copilot, or any AI agent
- **Persistent**: Survives session boundaries and agent restarts
- **Shareable**: Supports multi-agent coordination and human review
- **Versioned**: Tracked in git (excluding sensitive/ephemeral content)

**What to Persist**:

| Category | Examples | Git Tracked? |
|----------|----------|--------------|
| **Project Context** | Architecture overview, key patterns, domain concepts | Yes |
| **Decisions** | Why a library was chosen, tradeoffs made, approaches rejected | Yes |
| **Learnings** | Discovered codebase patterns, gotchas, conventions | Yes |
| **Handoffs** | Context for next agent session, open questions, next steps | Yes |
| **Session State** | Current task progress, temporary notes | No (ephemeral) |
| **Sensitive Info** | API keys, credentials, personal data | No (gitignored) |

**Sample .gitignore**:
```
# Ephemeral session data
.ai/memory/session/*
!.ai/memory/session/.gitkeep

# Sensitive information
.ai/secrets/
*.credentials
```

**Multi-Agent Handoff Protocol**:

1. **Outgoing Agent**: Before ending session, write to `handoffs/`:
   - Summary of work completed
   - Open questions or blockers
   - Recommended next steps
   - Any context the next agent needs

2. **Incoming Agent**: On session start, read:
   - `context/project-summary.md` for overall context
   - `memory/decisions/` for key architectural decisions
   - `memory/handoffs/` for immediate context from previous agent
   - `memory/learnings/` for codebase-specific knowledge

**Implementation Notes**:
- Create `.ai/` folder at project root (alongside existing AOSD/ folder)
- Use markdown files for human readability and easy AI consumption
- Structure files with clear headers for efficient context loading
- Review memory files periodically to prune stale content
- Consider agent-specific subfolders for multi-agent projects

**Integration with AOSD**:
- Decisions in `.ai/memory/decisions/` complement ADRs in `AOSD/decisions/`
- Memory files support Principle 1 (Multi-Agent Orchestration) by enabling context sharing
- Supports Goal 1 (Sustainability) by reducing human-dependency debt

---

## Pattern 8: Infrastructure-as-Code Invariant Enforcement

**Problem**: Architectural invariants (Principle 4) are often documented but not automatically enforced. AI agents and developers can inadvertently deploy infrastructure that violates security policies, resource limits, or organizational standards—violations that may not surface until production incidents.

**Solution**: Encode invariants directly into your infrastructure-as-code pipeline using policy-as-code tools that:
- Validate infrastructure definitions before deployment
- Block deployments that violate invariants
- Provide immediate, actionable feedback to developers and AI agents
- Create an audit trail of policy evaluations

**Supports Principles**:
- **Principle 2 (Trustworthy-by-Design)**: Security and compliance enforced at infrastructure layer
- **Principle 4 (Architectural Invariants)**: Automated enforcement prevents drift

**Tool Examples** (platform-agnostic):

| Tool | Platform | Enforcement Point |
|------|----------|-------------------|
| **CDK Aspects** | AWS CDK | Compile-time validation of CDK constructs |
| **Open Policy Agent (OPA)** | Any (Kubernetes, Terraform, etc.) | Policy evaluation against JSON/YAML |
| **HashiCorp Sentinel** | Terraform Enterprise/Cloud | Pre-apply policy checks |
| **Pulumi CrossGuard** | Pulumi | Policy packs validated at preview/up |
| **Azure Policy** | Azure | Runtime and deployment-time enforcement |
| **Config Connector** | GCP | Kubernetes-native policy enforcement |

**Common Invariants to Enforce**:

| Category | Example Invariants |
|----------|-------------------|
| **Security** | All storage encrypted at rest; No public S3 buckets; Secrets from vault only |
| **Networking** | No 0.0.0.0/0 ingress; Private subnets for databases; TLS 1.2+ required |
| **Cost Control** | Instance types from approved list; Max resources per environment |
| **Compliance** | Logging enabled on all resources; Tags required; Retention policies set |
| **Reliability** | Multi-AZ for production; Backup policies configured; Health checks defined |

**Implementation Approach**:

1. **Document**: Translate architectural invariants into policy rules
2. **Encode**: Implement rules in your policy-as-code tool of choice
3. **Integrate**: Add policy evaluation to CI/CD pipeline as a blocking gate
4. **Alert**: Surface violations clearly with remediation guidance
5. **Iterate**: Add new policies as invariants evolve; track violations to find gaps

**Example Policy Structure** (conceptual):
```
policy "storage-encryption-required" {
  description = "All storage resources must have encryption enabled"
  enforcement = "hard-mandatory"

  rule = storage_resources.all(resource =>
    resource.encryption.enabled == true
  )

  remediation = "Set encryption = { enabled: true } on the resource"
}
```

**AI Agent Integration**:
- Include active policies in AI agent context so they can generate compliant infrastructure
- When policy violations occur, feed the violation message back to the AI agent for self-correction
- Document policy intent (not just rules) so AI agents understand the "why" behind constraints

**Implementation Notes**:
- Start with security-critical invariants; expand coverage incrementally
- Use "warn" mode during rollout before switching to "deny"
- Test policies against known-good and known-bad infrastructure samples
- Version control policies alongside infrastructure code
- Create a policy exception process for legitimate edge cases (with human approval)

**Relationship to Other Patterns**:
- Works with Pattern 1 (Environment Factory) to apply environment-specific policies
- Complements Pattern 2 (API Security Layer) by extending security controls to infrastructure
- Supports Pattern 7 (Agent Context Memory) by documenting policy decisions and learnings

---

## Pattern 9: AI Evaluation and Continuous Validation

**Problem**: AI systems (both development-time agents and runtime features) can degrade silently. Models drift as data distributions change, costs creep up unnoticed, and correctness issues may not surface until they cause user-facing problems. Traditional testing alone is insufficient for probabilistic AI outputs.

**Solution**: Implement continuous AI evaluation across four dimensions—correctness, robustness, cost, and drift—with automated monitoring and human-in-the-loop review triggers.

**Supports Goals**:
- **Goal 1 (Sustainability)**: Early drift detection prevents gradual system degradation
- **Goal 2 (Security)**: Robustness testing catches adversarial and edge-case failures
- **Goal 4 (Low Cost)**: Cost monitoring prevents runaway AI spending

**The Four Evaluation Dimensions**:

| Dimension | What It Measures | Key Questions |
|-----------|------------------|---------------|
| **Correctness** | Does the AI produce accurate, valid outputs? | Are responses factually correct? Do they follow constraints? |
| **Robustness** | Does the AI handle edge cases and adversarial inputs? | What happens with unusual inputs? Malicious prompts? |
| **Cost** | Is AI usage economically sustainable? | Cost per query? Total spend trending? Token efficiency? |
| **Drift** | Are AI behaviors changing over time? | Output distribution shifts? Quality degradation? New failure modes? |

### Correctness Evaluation

**Approaches**:
- **Ground truth comparison**: Compare AI outputs against known-correct answers for test cases
- **Constraint validation**: Verify outputs meet format, schema, and business rule requirements
- **Human evaluation sampling**: Periodic human review of random output samples
- **LLM-as-judge**: Use a separate model to evaluate output quality (with calibration against human judgments)

**Metrics**:
- Accuracy against labeled test sets
- Constraint satisfaction rate
- Human evaluation scores (usefulness, accuracy, completeness)
- Inter-rater reliability for subjective assessments

### Robustness Evaluation

**Approaches**:
- **Edge case testing**: Systematically test boundary conditions and unusual inputs
- **Adversarial testing**: Test with inputs designed to cause failures (prompt injection, jailbreaks)
- **Chaos engineering for AI**: Introduce latency, partial failures, malformed inputs
- **Distribution shift testing**: Test with inputs that differ from training/typical distribution

**Metrics**:
- Failure rate under adversarial conditions
- Graceful degradation rate (fails safely vs. catastrophically)
- Recovery time after transient failures
- Consistency across semantically equivalent inputs

### Cost Evaluation

**Approaches**:
- **Per-request cost tracking**: Instrument every AI call with cost attribution
- **Budget alerting**: Set thresholds and alert when costs exceed expectations
- **Efficiency analysis**: Identify expensive operations that could be optimized (caching, batching, smaller models)
- **Cost-per-outcome tracking**: Measure cost relative to value delivered (cost per successful task, not just cost per token)

**Metrics**:
- Cost per request/query (by model, feature, user segment)
- Total daily/weekly/monthly AI spend
- Cost variance from baseline
- Cost efficiency ratio (value delivered / cost incurred)

**Cost Red Flags** (from AOSD Cost Metrics):
- Repeated calls with identical or near-identical prompts (caching opportunity)
- Large context windows for simple tasks (right-sizing opportunity)
- Synchronous AI calls blocking user interactions (async opportunity)
- Premium model usage for tasks a smaller model could handle (model selection opportunity)

### Drift Detection

**Approaches**:
- **Output distribution monitoring**: Track statistical properties of outputs over time
- **Performance trending**: Monitor evaluation metrics for gradual degradation
- **Behavioral fingerprinting**: Compare current behavior against baseline behavior signatures
- **A/B comparison**: Periodically compare current model against reference baseline

**Metrics**:
- Statistical distance between current and baseline output distributions
- Rolling average of correctness metrics (with trend analysis)
- Anomaly detection alerts for sudden behavior changes
- User feedback trends (complaints, satisfaction scores)

### Implementation Approach

1. **Instrument**: Add logging and metrics collection to all AI interactions
2. **Baseline**: Establish baseline metrics for each dimension during initial deployment
3. **Monitor**: Set up dashboards and alerting for key metrics
4. **Evaluate**: Run periodic evaluation campaigns (automated tests + human review)
5. **Respond**: Define response procedures for metric threshold breaches

**Evaluation Cadence**:

| Evaluation Type | Frequency | Automation Level |
|-----------------|-----------|------------------|
| Correctness sampling | Daily | Automated + weekly human review |
| Robustness testing | Weekly (full suite) | Automated |
| Cost monitoring | Continuous | Automated alerting |
| Drift detection | Continuous | Automated with human investigation triggers |
| Comprehensive audit | Monthly/Quarterly | Human-led with automated support |

**Integration with HITL (Principle 8)**:
- Low drift / low anomaly: HITL Level 4-5 (AI autonomous)
- Moderate anomalies: HITL Level 3 (AI executes with approval)
- High drift / evaluation failures: HITL Level 1-2 (human review required)

**Reference**: For comprehensive coverage of AI evaluation methodology, see *AI Engineering* by Chip Huyen, which provides in-depth treatment of evaluation frameworks, metrics design, and production ML system monitoring.

**Implementation Notes**:
- Start with correctness and cost; add robustness and drift as system matures
- Use evaluation results to trigger model updates, prompt refinements, or feature flags
- Store evaluation artifacts for retrospective analysis and compliance
- Consider separate evaluation environments for intensive testing
- Document evaluation methodology in AOSD/ folder alongside ADRs

**Relationship to Other Patterns**:
- Extends Pattern 7 (Agent Context Memory) with evaluation insights stored in `.ai/memory/learnings/`
- Supports Pattern 8 (IaC Invariant Enforcement) by validating AI doesn't generate policy-violating infrastructure
- Complements observability infrastructure (see Observability for AI-Generated Code section)
