# Multi-Tenant DynamoDB Pattern - Reference Example

**Pattern**: Multi-Tenant Data Isolation (AOSD Framework)
**Platform**: AWS DynamoDB
**Purpose**: Prevent data leakage between tenants in shared infrastructure

---

## Problem

Multi-tenant SaaS applications need to:
- Isolate tenant data completely
- Prevent accidental cross-tenant queries
- Validate tenant access on every request
- Support efficient queries within a tenant

---

## Solution

Design DynamoDB keys with explicit tenant identifiers:
- Partition keys include tenant ID
- Validate tenant ID at handler boundary
- Prohibit cross-tenant queries
- Use consistent key patterns

---

## Example Implementation

### Key Pattern Design

```python
"""
DynamoDB key patterns for multi-tenant data
"""

def tenant_key(tenant_id: str, entity_type: str, entity_id: str) -> str:
    """
    Create partition key with tenant scoping

    Examples:
    - TENANT#acme-corp#USER#12345
    - TENANT#globex#DOCUMENT#98765
    """
    return f"TENANT#{tenant_id}#{entity_type}#{entity_id}"

def version_key(version_id: str) -> str:
    """
    Create sort key for versioned data

    Examples:
    - VERSION#v1.0
    - VERSION#2024-01-15
    """
    return f"VERSION#{version_id}"

def metadata_key() -> str:
    """Sort key for entity metadata"""
    return "METADATA"
```

### Data Access Layer

```python
"""
Multi-tenant data access with validation
"""
from src.common.aws_factory import get_dynamodb_client
from src.common.config import get_config

class TenantDataAccess:
    """Base class for tenant-scoped data access"""

    def __init__(self, tenant_id: str):
        """
        Initialize with tenant ID

        Args:
            tenant_id: Validated tenant identifier
        """
        if not tenant_id:
            raise ValueError("Tenant ID required")

        self.tenant_id = tenant_id
        self.dynamodb = get_dynamodb_client()
        self.config = get_config()

    def get_user(self, user_id: str) -> dict:
        """
        Get user for THIS tenant only

        Args:
            user_id: User identifier

        Returns:
            User data if found

        Raises:
            ValueError: If user not found or access denied
        """
        pk = tenant_key(self.tenant_id, "USER", user_id)

        response = self.dynamodb.get_item(
            TableName=self.config.main_table_name,
            Key={
                'PK': {'S': pk},
                'SK': {'S': metadata_key()}
            }
        )

        if 'Item' not in response:
            raise ValueError(f"User {user_id} not found")

        return self._deserialize(response['Item'])

    def list_users(self, limit: int = 100) -> list:
        """
        List users for THIS tenant only

        Args:
            limit: Maximum number of users to return

        Returns:
            List of user data

        Note: Cannot query across tenants
        """
        # Query by partition key prefix - scoped to tenant
        pk_prefix = f"TENANT#{self.tenant_id}#USER#"

        response = self.dynamodb.query(
            TableName=self.config.main_table_name,
            KeyConditionExpression='begins_with(PK, :pk_prefix)',
            ExpressionAttributeValues={
                ':pk_prefix': {'S': pk_prefix}
            },
            Limit=limit
        )

        return [self._deserialize(item) for item in response.get('Items', [])]

    def _deserialize(self, item: dict) -> dict:
        """Convert DynamoDB item to Python dict"""
        # Simplified - real implementation would handle all types
        return {
            k: list(v.values())[0]
            for k, v in item.items()
        }
```

### Lambda Handler with Tenant Validation

```python
"""
API Gateway handler with tenant validation
"""
import json
from src.data.tenant_access import TenantDataAccess

def handler(event, context):
    """
    Handle API request with tenant validation

    Tenant ID comes from:
    - JWT claims (preferred)
    - API key mapping
    - Request context
    """
    # Extract tenant ID from authenticated context
    tenant_id = extract_tenant_id(event)

    if not tenant_id:
        return {
            'statusCode': 403,
            'body': json.dumps({'error': 'Tenant ID required'})
        }

    # Validate tenant ID is authorized
    if not is_tenant_authorized(tenant_id, event):
        return {
            'statusCode': 403,
            'body': json.dumps({'error': 'Unauthorized tenant'})
        }

    # All data access scoped to this tenant
    data_access = TenantDataAccess(tenant_id)

    # Extract user ID from path
    user_id = event['pathParameters']['userId']

    try:
        user = data_access.get_user(user_id)
        return {
            'statusCode': 200,
            'body': json.dumps(user)
        }
    except ValueError as e:
        return {
            'statusCode': 404,
            'body': json.dumps({'error': str(e)})
        }

def extract_tenant_id(event: dict) -> str:
    """
    Extract tenant ID from request context

    Options:
    1. JWT claims: event['requestContext']['authorizer']['claims']['tenant_id']
    2. Custom header: event['headers']['X-Tenant-ID']
    3. API key mapping: look up tenant from API key
    """
    # Example: From JWT claims
    claims = event.get('requestContext', {}).get('authorizer', {}).get('claims', {})
    return claims.get('tenant_id')

def is_tenant_authorized(tenant_id: str, event: dict) -> bool:
    """
    Validate tenant is authorized for this request

    Checks:
    - Tenant exists and is active
    - User belongs to tenant
    - Subscription is valid
    """
    # Simplified - real implementation would check database
    return bool(tenant_id)
```

### Example Table Structure

```
DynamoDB Table: main-table
- PK (String): Partition Key
- SK (String): Sort Key
- Attributes: Various

Example Items:
┌────────────────────────────────────┬──────────────┬─────────────────┐
│ PK                                 │ SK           │ Data            │
├────────────────────────────────────┼──────────────┼─────────────────┤
│ TENANT#acme-corp#USER#user-123     │ METADATA     │ {name: "Alice"} │
│ TENANT#acme-corp#USER#user-456     │ METADATA     │ {name: "Bob"}   │
│ TENANT#acme-corp#DOC#doc-789       │ METADATA     │ {title: "X"}    │
│ TENANT#globex#USER#user-111        │ METADATA     │ {name: "Carol"} │
│ TENANT#globex#USER#user-222        │ METADATA     │ {name: "Dave"}  │
└────────────────────────────────────┴──────────────┴─────────────────┘

Note: Queries by PK prefix isolate tenants automatically
```

---

## Security Guarantees

### Partition Key Scoping
✅ **Safe**: Query by `TENANT#acme-corp#USER#` prefix
❌ **Unsafe**: Scan entire table
❌ **Unsafe**: Query without tenant prefix

### Tenant Validation
- **Every request** validates tenant ID
- **Before** any data access
- **No exceptions** for admin users (they switch tenant context)

### Cross-Tenant Access
- **Prohibited** by design
- Keys prevent accidental cross-tenant reads
- Validation layer catches attempts

---

## Testing Multi-Tenancy

```python
"""
Test tenant isolation
"""
import pytest
from moto import mock_dynamodb
from src.data.tenant_access import TenantDataAccess, tenant_key, metadata_key

@mock_dynamodb
def test_tenant_isolation():
    """Verify tenants cannot access each other's data"""
    # Setup
    dynamodb = get_dynamodb_client()
    create_test_table(dynamodb)

    # Create data for tenant A
    tenant_a = TenantDataAccess("tenant-a")
    tenant_a.create_user("user-1", {"name": "Alice"})

    # Create data for tenant B
    tenant_b = TenantDataAccess("tenant-b")
    tenant_b.create_user("user-1", {"name": "Bob"})

    # Verify isolation
    user_from_a = tenant_a.get_user("user-1")
    assert user_from_a['name'] == "Alice"

    user_from_b = tenant_b.get_user("user-1")
    assert user_from_b['name'] == "Bob"

    # Verify tenant A cannot see tenant B's users
    users_in_a = tenant_a.list_users()
    assert len(users_in_a) == 1
    assert all(u['name'] != "Bob" for u in users_in_a)
```

---

## AI Agent Guardrails

**AI Must**:
- Include tenant ID in all partition keys
- Validate tenant ID at handler boundary
- Use `TenantDataAccess` class or similar
- Never query without tenant scoping

**AI Must Not**:
- Create keys without tenant prefix
- Allow cross-tenant queries
- Skip tenant validation
- Use table scans in production code

**Testing**:
- Every data access test must verify tenant isolation
- Test that tenant A cannot access tenant B's data

---

## Variations

### Alternative Approaches

**Database-Per-Tenant**:
- Pros: Complete isolation
- Cons: Operational complexity, cost

**Schema-Per-Tenant** (PostgreSQL):
- Pros: Good isolation, shared infrastructure
- Cons: Schema management

**Row-Level Security** (PostgreSQL/SQL):
- Pros: Database-enforced
- Cons: Requires RLS-capable database

This DynamoDB approach:
- ✅ Scales to many tenants
- ✅ Cost-effective
- ✅ Simple to implement
- ✅ Query-efficient

---

**End of Multi-Tenant DynamoDB Pattern Example**
