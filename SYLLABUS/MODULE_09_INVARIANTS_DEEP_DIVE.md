# Module 09 — Invariants Deep Dive
**AOSD Curriculum**
**Understanding and Designing Architectural Invariants**

---

## 1. Purpose of This Module

This module provides a comprehensive deep-dive into **architectural invariants**—the non-negotiable rules that make AOSD safe, predictable, and maintainable.

**Important**: Invariants are **implementation-specific**. The examples in this module use an AWS serverless reference implementation to teach invariant design principles. Your invariants will differ based on your platform, architecture, and requirements. The goal is to learn *how* to design invariants, not to copy these specific ones.

You will learn:

- Why each invariant exists
- What problems each invariant solves
- How to apply each invariant in practice
- How to recognize invariant violations
- When and how to evolve invariants
- How invariants interact with each other
- How to enforce invariants through AI prompts and architecture

Invariants are the **foundation** of AOSD. They're not optional guidelines—they're **architectural fitness functions** that ensure system integrity.

---

# 2. What Are Invariants?

**Definition**: Invariants are **rules that never change**, regardless of feature, timeline, or pressure.

Invariants provide:

- **Stability** in a rapidly changing AOSD environment
- **Safety** when AI is generating code at high velocity
- **Predictability** for multi-agent collaboration
- **Auditability** for compliance and security reviews
- **Evolvability** by creating stable boundaries

**Key insight**: AI moves fast. Invariants are the anchor that prevents drift.

---

# 3. Example Invariant Set: AWS Serverless

The following 10 invariants demonstrate a complete invariant set for an AWS serverless architecture. Use these as a **template for designing your own invariants**, adapted to your specific platform and requirements.

1. AWS Factory Pattern
2. Lambda Bundling Pattern
3. Multi-Tenant DDB Pattern
4. WAF-First Security Pattern
5. Decision System Pattern
6. Revision Instructions Pattern
7. SSM-Only Access Pattern
8. Three-Tier Test Strategy
9. AI Prompt Contracts
10. Invariant Evolution Protocol

**Note**: Some invariants (like Three-Tier Test Strategy and AI Prompt Contracts) are broadly applicable. Others (like AWS Factory Pattern) are platform-specific and would need adaptation for Azure, GCP, or on-premises deployments.

---

# 4. Invariant 1: AWS Factory Pattern

**Reference**: Document this invariant in your project's invariants file

### 4.1 Purpose
Eliminate raw `boto3.client()` / `boto3.resource()` calls.

Centralize configuration, retries, and environment-specific logic.

### 4.2 The Rule
**No handler, service, module, or script may call `boto3.client()` or `boto3.resource()` directly.**

All AWS interactions must go through factory functions in `src/common/aws.py`.

### 4.3 Why This Matters
**Without AWS Factory**:
- Retries configured inconsistently
- Timeouts vary unpredictably
- Tests pollute each other
- Mocking is complex and fragile
- Credential leakage risk
- Environment switching is impossible

**With AWS Factory**:
- One place to update retry logic
- Deterministic test behavior
- Easy mocking via `@lru_cache` reset
- Environment switching is trivial
- All clients properly configured

### 4.4 Recognizing Violations
**Violation**:
```python
# In handler or service module
dynamodb = boto3.client('dynamodb')  # WRONG!
```

**Correct**:
```python
from src.common.aws import get_dynamodb

dynamodb = get_dynamodb()  # RIGHT
```

### 4.5 How to Apply
1. Never write `boto3.client()` or `boto3.resource()` in application code
2. Always import from `src.common.aws`
3. If new AWS service needed, add factory function to `aws.py`
4. AI agents must auto-refactor violations

### 4.6 Testing Integration
- `_boto_guard` fixture fails tests that bypass factory
- `_reset_aws_singletons` clears cached clients between tests
- moto works seamlessly with factory pattern

---

# 5. Invariant 2: Lambda Bundling Pattern

**Reference**: Document this invariant in your project's invariants file

### 5.1 Purpose
Ensure Lambdas can import correctly, bundle dependencies properly, and deploy reliably.

### 5.2 The Rule
Every Lambda must:
- Have a dedicated handler file in `src/lambdas/[name]/handler.py`
- Bundle only necessary dependencies
- Not import from other Lambda directories
- Use shared code via `src/common/` or `src/services/`

### 5.3 Why This Matters
**Without Bundling Pattern**:
- Import errors at runtime (works locally, fails in Lambda)
- Oversized deployment packages
- Slow cold starts
- Cross-Lambda coupling
- Deployment failures

**With Bundling Pattern**:
- Clean imports guaranteed
- Minimal package sizes
- Fast cold starts
- Clear Lambda boundaries
- Reliable deployments

### 5.4 Recognizing Violations
**Violation**:
```python
# In src/lambdas/control_handler/handler.py
from src.lambdas.gap_handler.logic import analyze_gap  # WRONG - cross-Lambda import
```

**Correct**:
```python
# Move shared logic to src/services/gap_analysis.py
from src.services.gap_analysis import analyze_gap  # RIGHT
```

### 5.5 How to Apply
1. Keep Lambda handlers thin (orchestration only)
2. Move shared logic to `src/common/` or `src/services/`
3. Run slow integration tests to validate bundle structure
4. Use Makefile targets for consistent bundling

---

# 6. Invariant 3: Multi-Tenant DDB Pattern

**Reference**: Document this invariant in your project's invariants file

### 6.1 Purpose
Prevent cross-tenant data leakage in multi-tenant SaaS applications.

### 6.2 The Rule
Every DynamoDB item must:
- Have `PK` starting with `CLIENT#{client_id}#...`
- Have `SK` scoped to tenant data
- Never query across clients without explicit aggregation logic
- Validate tenant ID on every handler entry
- Log all queries with tenant ID

### 6.3 Why This Matters
**Without Multi-Tenant Pattern**:
- Accidental cross-tenant data exposure
- Compliance violations (SOC2, ISO 27001)
- Security incidents
- Customer trust loss
- Legal liability

**With Multi-Tenant Pattern**:
- Guaranteed tenant isolation
- Audit-friendly access patterns
- Query optimization via PK/SK
- Compliance-ready architecture

### 6.4 Recognizing Violations
**Violation**:
```python
# Missing tenant validation
def get_control(control_id):
    return dynamodb.get_item(
        TableName="Controls",
        Key={"PK": {"S": f"CONTROL#{control_id}"}}  # WRONG - no CLIENT# prefix
    )
```

**Correct**:
```python
def get_control(client_id, control_id):
    # Validate tenant ID first
    validate_client_id(client_id)

    return dynamodb.get_item(
        TableName="Controls",
        Key={"PK": {"S": f"CLIENT#{client_id}#CONTROL#{control_id}"}}  # RIGHT
    )
```

### 6.5 How to Apply
1. Always include `client_id` in handler parameters
2. Validate tenant ID at handler entry
3. Prefix all PKs with `CLIENT#{client_id}#`
4. Test cross-tenant confusion scenarios
5. Log all queries with tenant ID for audit

---

# 7. Invariant 4: WAF-First Security Pattern

**Reference**: Document this invariant in your project's invariants file

### 7.1 Purpose
Ensure all API endpoints are protected by AWS WAF before they handle requests.

### 7.2 The Rule
- Every API Gateway must have WAF attached
- No API can be deployed without WAF
- WAF rules must block common attacks (SQL injection, XSS, rate limiting)
- CloudWatch logs must capture blocked requests

### 7.3 Why This Matters
**Without WAF**:
- API exposed to automated attacks
- DDoS vulnerabilities
- SQL injection risk
- XSS risk
- No rate limiting
- No IP blocking

**With WAF**:
- Defense in depth
- Automated attack mitigation
- Compliance requirement met (NIST 800-53 SC-7)
- Observable attack patterns
- Reduced Lambda invocation costs (attacks blocked before reaching Lambda)

### 7.4 Recognizing Violations
**Violation**:
- API Gateway deployed without WAF association
- WAF rules disabled or misconfigured
- WAF logs not monitored

**Correct**:
- Every API has WAF ARN in CDK config
- WAF rules include: SQL injection, XSS, rate limiting, geo-blocking (if needed)
- CloudWatch Logs capture all WAF actions

### 7.5 How to Apply
1. Define WAF rules in CDK infrastructure
2. Associate WAF with API Gateway
3. Enable WAF logging to CloudWatch
4. Monitor WAF metrics (blocked requests, allowed requests)
5. Regularly review and update WAF rules

---

# 8. Invariant 5: Decision System Pattern

**Reference**: Document this invariant in your project's invariants file

### 8.1 Purpose
Parameterize behavior without code changes. Enable per-tenant customization.

### 8.2 The Rule
- All configurable behavior stored in DynamoDB "decisions catalog"
- Decisions retrieved via precedence system (tenant → framework → default)
- Never hardcode behavior that varies by client
- Decisions are versioned and auditable

### 8.3 Why This Matters
**Without Decision System**:
- Code changes for every client customization
- Deployment risk for minor tweaks
- No audit trail of behavior changes
- Client-specific code branches (nightmare maintenance)

**With Decision System**:
- Zero-code customization
- Audit-friendly (all decisions logged)
- Precedence makes defaults safe
- Behavior changes without deployment

### 8.4 Example
```python
# WRONG - hardcoded
def generate_control_text(control_id):
    tone = "formal"  # What if client wants "friendly"?
    ...

# RIGHT - decision-driven
def generate_control_text(client_id, control_id):
    decisions = get_precedence_decisions(client_id)
    tone = decisions.get("control_tone", "formal")  # Defaults to "formal"
    ...
```

### 8.5 How to Apply
1. Identify any behavior that might vary per client
2. Store it in decisions catalog
3. Use precedence system for retrieval
4. Log decision usage for audit
5. Version decisions when they change

---

# 9. Invariant 6: Revision Instructions Pattern

**Reference**: Document this invariant in your project's invariants file

### 9.1 Purpose
Enable human-in-the-loop corrections to AI-generated outputs without regenerating.

### 9.2 The Rule
- AI-generated content can be revised via "revision instructions"
- Original output + instructions stored together
- AI uses instructions on next generation
- Revisions are versioned and traceable

### 9.3 Why This Matters
**Without Revision Instructions**:
- User edits lost on next generation
- No feedback loop for AI improvement
- Manual editing required repeatedly
- AI doesn't learn from corrections

**With Revision Instructions**:
- User corrections guide future generations
- Audit trail of all changes
- Progressive refinement
- Human expertise captured

### 9.4 Example
User generates control text via AI. User feedback: "Too technical, add examples."

```python
# Store revision instruction
revision = {
    "PK": f"CLIENT#{client_id}#CONTROL#{control_id}",
    "SK": f"REVISION#{revision_id}",
    "original_output": "...",
    "instruction": "Make less technical, add examples",
    "revised_output": "...",
    "timestamp": now()
}
```

Next generation uses instruction in prompt:
```python
prompt += f"\nRevision instruction from user: {instruction}"
```

### 9.5 How to Apply
1. Provide revision UI for AI-generated content
2. Store instructions with outputs
3. Include instructions in future prompts
4. Version revisions
5. Allow users to accept/reject revised outputs

---

# 10. Invariant 7: SSM-Only Access Pattern

**Reference**: Document this invariant in your project's invariants file

### 10.1 Purpose
Eliminate SSH access. Enforce secure, auditable, MFA-protected access to EC2 instances.

### 10.2 The Rule
- No SSH keys
- No SSH port 22 open
- All EC2 access via AWS Systems Manager Session Manager
- MFA required for all sessions
- All sessions logged to CloudWatch

### 10.3 Why This Matters
**Without SSM-Only**:
- SSH key management burden
- SSH keys can be stolen
- No MFA enforcement
- Weak audit trail
- Port 22 attack surface

**With SSM-Only**:
- No keys to manage or steal
- MFA enforced automatically
- Every session logged and auditable
- Compliance-ready (NIST 800-53 AC-17)
- Zero inbound ports open

### 10.4 Recognizing Violations
**Violation**:
- SSH port 22 open in security group
- SSH keys generated for EC2
- Direct SSH access used

**Correct**:
- Security group allows no inbound SSH
- EC2 has SSM agent installed
- Access via `aws ssm start-session --target i-xxxxx`

### 10.5 How to Apply
1. Remove SSH port from security groups
2. Install SSM agent on EC2 instances
3. Configure IAM roles for SSM access
4. Require MFA for IAM users
5. Enable session logging to CloudWatch
6. Train users on `aws ssm start-session` command

---

# 11. Invariant 8: Three-Tier Test Strategy

**Reference**: See [Testing Strategy](../MANUAL/AOSD_FRAMEWORK.md#7-testing-strategy-fastmediumslow) in the framework

### 11.1 Purpose
Balance speed, coverage, and confidence through layered testing.

### 11.2 The Rule
- **Fast tests** (pure functions, no AWS, ~2-3 minutes for whole suite)
- **Medium tests** (mocked AWS via moto, ~5 minutes)
- **Slow tests** (integration, bundle validation, ~2 minutes)
- **Playwright** (E2E, Jim's workflow)

### 11.3 Why This Matters
**Without Three-Tier**:
- All tests slow → developers skip tests
- All tests fast → miss integration issues
- Unpredictable test runtime
- Poor signal-to-noise ratio

**With Three-Tier**:
- Fast feedback loop
- Comprehensive coverage
- Predictable runtime
- Clear test boundaries

### 11.4 How to Apply
1. Extract pure functions → write fast tests
2. Test handler logic → write medium tests with mocked AWS
3. Test bundle structure & DDB → write slow integration tests
4. Validate end-to-end flows → Playwright from laptop

**See MODULE_03** for complete TDD workflow.

---

# 12. Invariant 9: AI Prompt Contracts

### 12.1 Purpose
Define clear, stable interfaces between humans and AI agents.

### 12.2 The Rule
- Each AI agent (ChatGPT, Claude, Sonnet) has a **prompt contract**
- Contract defines: role, capabilities, constraints, input/output format
- Humans use same contract language across sessions
- Contracts versioned and stored in TEMPLATES/

### 12.3 Why This Matters
**Without Prompt Contracts**:
- Inconsistent AI behavior across sessions
- Unclear role boundaries
- Wasted tokens on repeated explanations
- Drift over time

**With Prompt Contracts**:
- Predictable AI behavior
- Clear role separation
- Efficient sessions
- Stable multi-agent collaboration

### 12.4 Example
**ChatGPT Prompt Contract** (TEMPLATES/PROMPT_CONTRACT_CHATGPT.md):
- Role: Architect / Reviewer
- Input: Slice spec or code to review
- Output: Architectural analysis, risk assessment, recommendations
- Constraints: No code generation, only architectural guidance

**Claude Code Prompt Contract** (TEMPLATES/PROMPT_CONTRACT_CLAUDE.md):
- Role: Builder / Implementer
- Input: Slice spec + TodoWrite approval
- Output: Code, tests, documentation
- Constraints: Must follow all AOSD invariants, must use TodoWrite

### 12.5 How to Apply
1. Use template prompt contracts from TEMPLATES/
2. Reference contract at start of each session
3. Enforce role boundaries
4. Update contracts when patterns evolve

---

# 13. Invariant 10: Invariant Evolution Protocol

### 13.1 Purpose
Ensure invariants can evolve safely without breaking existing systems.

### 13.2 The Rule
- Invariants are **not immutable forever**
- Changes require:
  1. Documented rationale
  2. Impact analysis across all projects
  3. Migration path
  4. Backward compatibility where possible
  5. Update documentation
  6. Notification to all affected projects

### 13.3 Why This Matters
**Without Evolution Protocol**:
- Invariants become constraints that prevent progress
- Framework ossifies
- Workarounds proliferate
- Disillusionment with AOSD

**With Evolution Protocol**:
- Framework adapts to new realities
- Changes are deliberate and safe
- Lessons learned incorporated
- Trust maintained

### 13.4 When to Evolve Invariants
**Good reasons**:
- New AWS service requires different pattern
- Compliance requirement changes
- Security vulnerability discovered
- Proven better approach emerges

**Bad reasons**:
- Convenience for single feature
- Avoiding discipline
- Shortcuts under pressure

### 13.5 How to Evolve
1. Propose change in GitHub issue (label: `invariant-evolution`)
2. Document rationale and alternatives considered
3. Analyze impact on existing projects
4. Define migration path
5. Update invariant documentation
6. Update MANUAL/ part
7. Notify all affected projects
8. Execute migration

**See**: [Architectural Invariants](../MANUAL/AOSD_FRAMEWORK.md#4-architectural-invariants) in the framework for guidance

---

# 14. How Invariants Interact

Invariants don't exist in isolation—they reinforce each other:

### 14.1 AWS Factory + Multi-Tenant DDB
- AWS Factory ensures consistent DDB client
- Multi-Tenant DDB uses factory-provided client
- Together: Predictable, isolated database access

### 14.2 Decision System + Revision Instructions
- Decision System parameterizes AI behavior
- Revision Instructions capture human corrections
- Together: AI adapts to client needs progressively

### 14.3 WAF + Multi-Tenant DDB
- WAF blocks attacks before they reach Lambda
- Multi-Tenant DDB prevents data leakage if attack succeeds
- Together: Defense in depth

### 14.4 SSM-Only + Three-Tier Tests
- SSM-Only ensures secure environment access
- Three-Tier Tests run in those environments
- Together: Secure, testable operations

### 14.5 Lambda Bundling + AWS Factory
- Lambda Bundling ensures clean imports
- AWS Factory centralizes client creation
- Together: Reliable, minimal Lambda packages

**Insight**: Violating one invariant often cascades to violate others.

---

# 15. Enforcing Invariants Through AI Prompts

AI agents must be **prompted to enforce invariants**:

### 15.1 Prompt Engineering for Invariants
**Bad prompt**:
> "Add a new Lambda handler for gap analysis."

**Good prompt**:
> "Add a new Lambda handler for gap analysis. Follow these AOSD invariants:
> - Use AWS Factory Pattern (import from src.common.aws)
> - Follow Multi-Tenant DDB Pattern (PK must start with CLIENT#{client_id})
> - Use Lambda Bundling Pattern (handler in src/lambdas/gap_analysis/handler.py)
> - Write Fast/Medium/Slow tests
> - Ensure WAF is attached to API
>
> Reference: Your project's AWS Factory and Multi-Tenant DDB invariant documentation"

### 15.2 Using Prompt Contracts
Prompt contracts (Invariant 9) automatically remind AI of invariants:

```
You are Claude Code, AOSD's builder agent.

Your role: Implement slices following all AOSD invariants.

Invariants you must enforce:
1. AWS Factory Pattern
2. Lambda Bundling Pattern
3. Multi-Tenant DDB Pattern
4. WAF Pattern
5. Decision System Pattern
6. Revision Instructions Pattern
7. SSM-Only Pattern
8. Three-Tier Test Strategy
9. AI Prompt Contracts
10. Invariant Evolution Protocol

Never violate these invariants. If uncertain, ask.
```

### 15.3 TodoWrite as Enforcement Checkpoint
TodoWrite forces AI to plan before coding. Plan includes invariant checks:

```
Todo:
- [ ] Verify AWS Factory usage
- [ ] Validate Multi-Tenant DDB PKs
- [ ] Check Lambda bundling structure
- [ ] Write Fast/Medium/Slow tests
- [ ] Confirm WAF attachment
```

---

# 16. Hands-On Exercises

### Exercise 1 — Invariant Violation Audit
Review one Lambda handler in your project.

Check for violations of each invariant:
1. AWS Factory: Any `boto3.client()` calls?
2. Lambda Bundling: Proper handler location? Clean imports?
3. Multi-Tenant DDB: PKs start with `CLIENT#`? Tenant validation?
4. WAF: API protected?
5. Decision System: Any hardcoded behavior?
6. Revision Instructions: Support for user corrections?
7. SSM-Only: No SSH access?
8. Three-Tier Tests: Fast/Medium/Slow coverage?
9. Prompt Contracts: Used in recent sessions?
10. Evolution Protocol: Recent changes documented?

Document findings.

### Exercise 2 — Apply Invariant to New Feature
Design a new feature (e.g., "export gap analysis as PDF").

For each invariant, answer:
- Does this invariant apply to this feature?
- How will you enforce it?
- What are the risks if violated?

### Exercise 3 — Invariant Evolution Proposal
Pick one invariant that feels constraining or outdated.

Draft an evolution proposal:
1. Current state
2. Proposed change
3. Rationale
4. Impact analysis
5. Migration path

(Don't actually change it—just practice the proposal process)

### Exercise 4 — Multi-Invariant Integration
Design a feature that touches multiple invariants simultaneously:
- User uploads document (Lambda Bundling, AWS Factory for S3)
- AI analyzes document (runtime AI invariants from MODULE_08)
- Results stored per-tenant (Multi-Tenant DDB)
- Configurable analysis depth (Decision System)
- User can revise analysis (Revision Instructions)

Map how invariants interact.

---

# 17. Completion Criteria

You've mastered this module when you can:

- Recite all 10 invariants from memory
- Explain the purpose and rationale of each invariant
- Recognize violations of each invariant in code
- Apply all invariants to new features
- Understand how invariants interact and reinforce each other
- Enforce invariants through AI prompts and TodoWrite
- Propose invariant evolutions using proper protocol
- Audit existing code for invariant compliance
- Use invariant documentation for quick reference
- Integrate invariants naturally into development workflow

Invariants are not constraints—they're **freedom through discipline**.

**Next up**:
**Module 10 — Framework Evolution & Meta-Governance**

---

**End of MODULE_09_INVARIANTS_DEEP_DIVE.md**
