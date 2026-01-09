# AWS Factory Pattern - Reference Example

**Pattern**: Environment Factory (AOSD Framework)
**Platform**: AWS
**Purpose**: Centralized AWS service client creation with environment awareness

---

## Problem

Direct instantiation of boto3 clients leads to:
- Inconsistent configuration across Lambda functions
- Difficulty testing (hard-coded AWS clients)
- Environment-specific credentials scattered throughout code
- No single point for retry/timeout configuration

---

## Solution

Create a factory pattern that:
- Centralizes all AWS client creation
- Manages environment-specific configuration
- Enables test isolation via dependency injection
- Prevents credential and region drift

---

## Example Implementation

### `src/common/aws_factory.py`

```python
"""
AWS Factory Pattern - Example Implementation
Centralized AWS client creation for testability and consistency
"""
import boto3
from typing import Optional
from .config import get_config

# Singleton storage for test isolation
_clients = {}
_resources = {}

def get_dynamodb_client():
    """Get DynamoDB client with environment-aware configuration"""
    if 'dynamodb' not in _clients:
        config = get_config()
        _clients['dynamodb'] = boto3.client(
            'dynamodb',
            region_name=config.aws_region,
            endpoint_url=config.dynamodb_endpoint  # Allows local/mock override
        )
    return _clients['dynamodb']

def get_s3_client():
    """Get S3 client with environment-aware configuration"""
    if 's3' not in _clients:
        config = get_config()
        _clients['s3'] = boto3.client(
            's3',
            region_name=config.aws_region,
            endpoint_url=config.s3_endpoint
        )
    return _clients['s3']

def get_bedrock_client():
    """Get Bedrock client for AI runtime features"""
    if 'bedrock-runtime' not in _clients:
        config = get_config()
        _clients['bedrock-runtime'] = boto3.client(
            'bedrock-runtime',
            region_name=config.aws_region
        )
    return _clients['bedrock-runtime']

def reset_clients():
    """Reset client cache - ONLY for testing"""
    global _clients, _resources
    _clients = {}
    _resources = {}
```

### `src/common/config.py`

```python
"""
Environment-aware configuration
Loads settings from environment variables
"""
import os
from dataclasses import dataclass

@dataclass
class Config:
    """Application configuration"""
    environment: str
    aws_region: str
    dynamodb_endpoint: Optional[str]
    s3_endpoint: Optional[str]

    # DynamoDB table names
    main_table_name: str

def get_config() -> Config:
    """Load configuration from environment"""
    env = os.environ.get('ENVIRONMENT', 'dev')

    return Config(
        environment=env,
        aws_region=os.environ.get('AWS_REGION', 'us-east-1'),

        # Local/test overrides
        dynamodb_endpoint=os.environ.get('DYNAMODB_ENDPOINT'),  # Set for LocalStack
        s3_endpoint=os.environ.get('S3_ENDPOINT'),

        # Table names with environment prefix
        main_table_name=os.environ.get('MAIN_TABLE_NAME', f'{env}-main-table')
    )
```

### Lambda Handler Example

```python
"""
Lambda handler using AWS Factory
Never imports boto3 directly
"""
from src.common.aws_factory import get_dynamodb_client
from src.common.config import get_config

def handler(event, context):
    """
    Lambda handler that uses factory pattern
    """
    # Get clients through factory
    dynamodb = get_dynamodb_client()
    config = get_config()

    # Use client
    response = dynamodb.get_item(
        TableName=config.main_table_name,
        Key={'PK': {'S': 'EXAMPLE#123'}}
    )

    return {
        'statusCode': 200,
        'body': 'Success'
    }
```

### Test Example

```python
"""
Test using factory pattern with mocks
"""
import pytest
from moto import mock_dynamodb
from src.common.aws_factory import get_dynamodb_client, reset_clients

@pytest.fixture(autouse=True)
def reset_aws_factory():
    """Reset factory before each test"""
    reset_clients()
    yield
    reset_clients()

@mock_dynamodb
def test_lambda_handler():
    """Test handler with mocked DynamoDB"""
    # Factory will use mocked DynamoDB
    dynamodb = get_dynamodb_client()

    # Create test table
    dynamodb.create_table(
        TableName='test-main-table',
        KeySchema=[{'AttributeName': 'PK', 'KeyType': 'HASH'}],
        AttributeDefinitions=[{'AttributeName': 'PK', 'AttributeType': 'S'}],
        BillingMode='PAY_PER_REQUEST'
    )

    # Test your handler
    # ...
```

---

## Benefits

1. **Testability**: Easy to mock/stub AWS services
2. **Consistency**: Single configuration point
3. **Environment Awareness**: Automatic dev/staging/prod config
4. **Test Isolation**: Reset between tests
5. **No Hard-Coding**: All config from environment

---

## AI Agent Guardrails

**AI Must**:
- Always use factory functions (never `boto3.client()` directly)
- Use `reset_clients()` in test fixtures
- Add new clients to factory if needed

**AI Must Not**:
- Import boto3 in Lambda handlers
- Hard-code regions or endpoints
- Skip test isolation

---

## Variations

- **Azure**: Use similar pattern with Azure SDK
- **GCP**: Apply to Google Cloud client libraries
- **Multi-Cloud**: Factory can abstract across providers

---

**End of AWS Factory Pattern Example**
