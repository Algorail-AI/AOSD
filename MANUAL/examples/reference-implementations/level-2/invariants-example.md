# Example Invariant Definitions

**Reference: How to define non-negotiable architectural rules**

---

## Overview

This example shows how to document invariants in a Level 2 Orchestration Profile. Invariants are non-negotiable rules that AI agents and developers must always follow.

---

## What Makes a Good Invariant

| Characteristic | Description | Example |
|----------------|-------------|---------|
| **Non-negotiable** | No exceptions without explicit approval | "All APIs must have authentication" |
| **Verifiable** | Can be checked automatically or manually | "No direct SDK usage outside factory" |
| **Specific** | Clear enough to apply unambiguously | "Tenant ID validated at handler entry" |
| **Justified** | Has a clear reason (security, reliability, etc.) | "Prevents data leakage between tenants" |

---

## Invariant Categories

### Security Invariants (S)

Rules that protect against security vulnerabilities.

### Operational Invariants (O)

Rules that ensure system reliability and observability.

### Tenant Invariants (T)

Rules for multi-tenant data isolation.

### Service Factory Invariants (SF)

Rules for consistent cloud service access.

---

## Example Invariants

### S1: Sensitive Data Encryption

**Category**: Security

**Statement**: All sensitive data must be encrypted at rest and in transit.

**Rationale**: Protects against data breaches and meets compliance requirements.

**Verification**:
- At rest: DynamoDB tables use AWS-managed encryption
- In transit: All APIs require HTTPS (enforced by API Gateway)
- Code review: No plaintext storage of PII

**Enforcement**:
- CI: CDK validation ensures encryption settings
- Runtime: API Gateway rejects HTTP connections
- Review: Security review required for data model changes

**Exceptions**: None permitted.

**Related AOSD Principles**: Principle 2 (Trustworthy-by-Design)

---

### S2: Least Privilege Access

**Category**: Security

**Statement**: All IAM roles and policies must follow least privilege principle.

**Rationale**: Limits blast radius of compromised credentials.

**Verification**:
- CI: IAM policy analysis for overly broad permissions
- Review: Security review for IAM changes
- Audit: Quarterly access review

**Enforcement**:
- PR: Block PRs with `*` resources without justification
- CDK: Use specific resource ARNs

**Example (Compliant)**:
```python
# Good: Specific table access
policy.add_statements(
    iam.PolicyStatement(
        actions=["dynamodb:GetItem", "dynamodb:PutItem"],
        resources=[table.table_arn]
    )
)
```

**Example (Violation)**:
```python
# Bad: Overly broad access
policy.add_statements(
    iam.PolicyStatement(
        actions=["dynamodb:*"],
        resources=["*"]  # VIOLATION: Too broad
    )
)
```

**Exceptions**: Must be documented in ADR with security review approval.

**Related AOSD Principles**: Principle 2 (Trustworthy-by-Design)

---

### O1: All Services Monitored

**Category**: Operational

**Statement**: Every deployed service must have monitoring and alerting configured.

**Rationale**: Enables rapid detection and response to issues.

**Required Monitoring**:
- Lambda: Error rate, duration, throttles
- API Gateway: 4xx/5xx rates, latency
- DynamoDB: Throttled requests, consumed capacity
- SQS: Queue depth, message age

**Required Alerts**:
- Error rate > 5% for 5 minutes
- Latency p99 > 3 seconds
- Queue depth > 1000 messages

**Verification**:
- CDK: Alarms defined in infrastructure code
- CI: Validation that alarms exist for all resources
- Weekly: Dashboard review

**Enforcement**:
- PR: Block deployment without alarm definitions
- Audit: Monthly alarm coverage check

**Exceptions**: Development environment may have relaxed thresholds.

**Related AOSD Principles**: Principle 2 (Trustworthy-by-Design)

---

### O2: Appropriate Log Retention

**Category**: Operational

**Statement**: All logs must be retained according to environment-specific policies.

**Retention Policies**:
| Environment | Retention | Rationale |
|-------------|-----------|-----------|
| Development | 7 days | Cost optimization |
| Staging | 30 days | Debugging support |
| Production | 90 days | Compliance, incident investigation |

**Verification**:
- CDK: Log group retention settings validated
- Audit: Quarterly retention review

**Enforcement**:
- CI: Check log group configurations
- Alert: Non-compliant retention detected

**Exceptions**: Compliance may require longer retention (documented in ADR).

**Related AOSD Principles**: Principle 2 (Trustworthy-by-Design)

---

### T1: Tenant ID Validation at Boundary

**Category**: Tenant Isolation

**Statement**: Every API request must validate tenant ID at the handler entry point.

**Rationale**: Prevents unauthorized access to other tenants' data.

**Implementation Pattern**:
```python
@validate_tenant  # Decorator validates tenant_id from token
async def handle_request(event: APIGatewayEvent) -> Response:
    tenant_id = event.tenant_id  # Set by decorator
    # All operations use tenant_id
    ...
```

**Verification**:
- CI: Static analysis for handler entry points
- Code review: Verify decorator usage
- Test: Attempt cross-tenant access (should fail)

**Enforcement**:
- PR: Block handlers without tenant validation
- Runtime: Reject requests with invalid/missing tenant

**Example (Compliant)**:
```python
@validate_tenant
async def get_user(event: APIGatewayEvent) -> Response:
    tenant_id = event.tenant_id
    user = await user_service.get(tenant_id, event.user_id)
    return Response(body=user.to_dict())
```

**Example (Violation)**:
```python
# VIOLATION: No tenant validation
async def get_user(event: APIGatewayEvent) -> Response:
    user_id = event.path_params["user_id"]
    user = await user_service.get(user_id)  # Missing tenant_id
    return Response(body=user.to_dict())
```

**Exceptions**: Public endpoints (documented list) may skip tenant validation.

**Related AOSD Principles**: Principle 2 (Trustworthy-by-Design), Principle 4 (Invariants)

---

### T2: Tenant ID in All Data Keys

**Category**: Tenant Isolation

**Statement**: Every DynamoDB key must include tenant ID as the partition key prefix.

**Rationale**: Enforces physical data isolation at the storage layer.

**Key Pattern**:
```
PK: TENANT#{tenant_id}#{entity_type}#{entity_id}
SK: (varies by entity)
```

**Verification**:
- CI: Validate data model definitions
- Code review: Check all put/get operations
- Test: Query patterns include tenant filtering

**Enforcement**:
- Data model: Schema validation
- Runtime: Factory validates key patterns

**Example (Compliant)**:
```python
# Good: Tenant in partition key
item = {
    "PK": f"TENANT#{tenant_id}#USER#{user_id}",
    "SK": "PROFILE",
    ...
}
```

**Example (Violation)**:
```python
# VIOLATION: No tenant in key
item = {
    "PK": f"USER#{user_id}",  # Missing tenant prefix
    "SK": "PROFILE",
    ...
}
```

**Exceptions**: Cross-tenant lookup tables (admin only, documented separately).

**Related AOSD Principles**: Principle 2 (Trustworthy-by-Design), Principle 4 (Invariants)

---

### SF1: Service Factory Required

**Category**: Service Factory

**Statement**: All AWS service access must go through the service factory.

**Rationale**: Ensures consistent configuration, testability, and environment awareness.

**Factory Location**: `src/common/factory.py`

**Verification**:
- CI: Grep for direct boto3 usage
- Code review: Check for factory usage

**Enforcement**:
- PR: Block direct boto3.client/resource calls
- IDE: Linter rule (if available)

**Example (Compliant)**:
```python
from common.factory import get_dynamodb_table

async def save_user(tenant_id: str, user: User):
    table = get_dynamodb_table("users")  # Factory handles config
    await table.put_item(Item=user.to_item())
```

**Example (Violation)**:
```python
import boto3

# VIOLATION: Direct SDK usage
dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('users')
```

**Exceptions**: The factory itself (one location).

**Related AOSD Principles**: Principle 3 (Environment Isolation), Principle 9 (FCIS)

---

### SF2: Environment-Aware Configuration

**Category**: Service Factory

**Statement**: All service configuration must be environment-aware through the factory.

**Rationale**: Prevents dev/staging/prod configuration confusion.

**Configuration Source**:
- Environment: `ENVIRONMENT` env variable (dev/staging/prod)
- Table names: `{env}-{table_name}` pattern
- Endpoints: Environment-specific where applicable

**Verification**:
- CI: Check for hardcoded environment values
- Test: Deploy to test environment and verify isolation

**Enforcement**:
- Factory: Reads environment and applies configuration
- PR: Block hardcoded environment strings

**Example (Compliant)**:
```python
# Factory handles environment
table = get_dynamodb_table("users")
# Returns: dev-users, staging-users, or prod-users
```

**Example (Violation)**:
```python
# VIOLATION: Hardcoded table name
table = dynamodb.Table('prod-users')  # Hardcoded environment
```

**Exceptions**: None.

**Related AOSD Principles**: Principle 3 (Environment Isolation)

---

## Invariant Documentation Template

Use this template when adding new invariants:

```markdown
### [ID]: [Short Name]

**Category**: [Security | Operational | Tenant | Service Factory | Custom]

**Statement**: [One sentence describing the rule]

**Rationale**: [Why this rule exists]

**Verification**:
- [How to check compliance]

**Enforcement**:
- [How the rule is enforced]

**Example (Compliant)**:
[Code or configuration example]

**Example (Violation)**:
[Code or configuration example]

**Exceptions**: [Any permitted exceptions and their approval process]

**Related AOSD Principles**: [Principle references]
```

---

## Quick Reference for AI Agents

### Security (S)
- **S1**: Encrypt all sensitive data (rest + transit)
- **S2**: Least privilege for all IAM policies

### Operational (O)
- **O1**: Monitor all services with alerts
- **O2**: Retain logs per environment policy

### Tenant (T)
- **T1**: Validate tenant ID at every handler entry
- **T2**: Include tenant ID in all data keys

### Service Factory (SF)
- **SF1**: Use factory for all AWS access (no direct SDK)
- **SF2**: Factory handles environment configuration

---

## Invariant Review Process

### Adding New Invariants

1. Identify the need (security incident, operational issue, etc.)
2. Draft invariant using template
3. Review with team for clarity and feasibility
4. Implement verification and enforcement
5. Add to invariants documentation
6. Communicate to all team members and AI agents

### Modifying Invariants

1. Document reason for change in ADR
2. Assess impact on existing code
3. Update invariant documentation
4. Update enforcement mechanisms
5. Communicate changes

### Removing Invariants

1. Document reason for removal in ADR
2. Verify removal doesn't introduce risk
3. Remove enforcement mechanisms
4. Update documentation
5. Communicate removal

---

## Relationship to AOSD Starter Invariants

These example invariants align with AOSD Framework starter invariants:

| AOSD Starter | This Profile |
|--------------|--------------|
| S1 (Encryption) | S1 |
| S2 (Least Privilege) | S2 |
| O1 (Monitoring) | O1 |
| O2 (Logging) | O2 |
| T1 (Tenant Validation) | T1 |
| T2 (Tenant Isolation) | T2 |
| SF1 (Factory Usage) | SF1 |
| SF2 (Environment Config) | SF2 |

Your profile may add organization-specific invariants beyond the starters.

---

**End of Example**
