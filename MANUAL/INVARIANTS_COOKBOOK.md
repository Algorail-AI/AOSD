# Invariants Cookbook

**Executable Fitness Functions for Enforcing Architectural Invariants**

---

## 1. Purpose

This cookbook provides **ready-to-use fitness functions** (executable checks) that validate architectural invariants in AOSD systems. Each fitness function includes:

- **What it validates**: The invariant being enforced
- **Why it matters**: Consequences of violation
- **How to check**: Executable command or script
- **How to repair**: Steps to fix violations
- **Agent rules**: AI agent guidance for running and repairing

**Connection to AOSD Principles**:
- **Principle 4 (Architectural Invariants)**: Non-negotiable rules that prevent AI drift
- **Pattern 8 (IaC Invariant Enforcement)**: Automated policy enforcement in pipelines

---

## 2. Fitness Function Categories

### Overview

| Category | Focus Area | Scope | Typical Tools |
|----------|-----------|-------|---------------|
| Code Structure | File organization, patterns | Code | grep, ast, custom scripts |
| Security | Auth, secrets, permissions | Code, Infrastructure | grep, static analysis, scanners |
| API/Interface | Contracts, compatibility | Code, Runtime | schema validators, type checkers |
| Data Flow | Isolation, boundaries | Code, Infrastructure | grep, dependency analysis |
| Test/Quality | Coverage, assertions | Code | pytest, jest, coverage tools |
| Dependencies | Versions, licenses | Packaging | package managers, audit tools |

### Scope Definitions

Invariant scope clarifies *where* a rule applies to prevent confusion:

| Scope | Applies To | Example |
|-------|-----------|---------|
| **Code** | Source code structure and patterns | "No raw boto3 calls" (use factory) |
| **Packaging** | Build artifacts and bundles | "Exclude boto3 from Lambda bundles" |
| **Infrastructure** | Cloud resources and config | "WAF on all API endpoints" |
| **Runtime** | Application behavior at execution | "Response time < 5 seconds" |

An invariant's scope affects how it's enforced: Code-scope invariants use static analysis; Packaging-scope uses build checks; Infrastructure-scope uses IaC policies.

---

## 3. Code Structure Invariants

### 3.1 No Direct Database Access from Handlers

**Invariant**: Handlers must use service layer, never access database directly.

**Why It Matters**: Direct database access bypasses business logic, validation, and audit logging.

**Check (grep)**:
```bash
# Find handler files with direct database imports
grep -rn "import.*dynamodb\|from.*db\|import.*sqlite\|import.*psycopg" \
  src/handlers/ --include="*.py"

# Expected: No matches
# Exit code 0 = violation found, exit code 1 = pass
```

**Check (Python AST)**:
```python
# invariant_checks/no_direct_db_in_handlers.py
import ast
import sys
from pathlib import Path

FORBIDDEN_IMPORTS = ['boto3.dynamodb', 'psycopg2', 'sqlite3', 'pymongo']

def check_file(filepath):
    with open(filepath) as f:
        tree = ast.parse(f.read())

    for node in ast.walk(tree):
        if isinstance(node, ast.Import):
            for alias in node.names:
                if any(forbidden in alias.name for forbidden in FORBIDDEN_IMPORTS):
                    return False, f"Direct DB import: {alias.name}"
        if isinstance(node, ast.ImportFrom):
            if node.module and any(forbidden in node.module for forbidden in FORBIDDEN_IMPORTS):
                return False, f"Direct DB import from: {node.module}"
    return True, None

def main():
    violations = []
    for path in Path('src/handlers').rglob('*.py'):
        ok, msg = check_file(path)
        if not ok:
            violations.append(f"{path}: {msg}")

    if violations:
        print("VIOLATIONS FOUND:")
        for v in violations:
            print(f"  - {v}")
        sys.exit(1)
    print("PASS: No direct database access in handlers")
    sys.exit(0)

if __name__ == '__main__':
    main()
```

**How to Repair**:
1. Move database operations to service layer
2. Import service functions instead of database clients
3. Ensure handlers call services, services call repositories

**Agent Rules**:
```
When implementing handlers:
- NEVER import database clients (boto3.dynamodb, psycopg2, etc.) in handler files
- ALWAYS import from services layer: `from services.user_service import get_user`
- If you need data access, create or use existing service function
- Before committing: run `python invariant_checks/no_direct_db_in_handlers.py`
```

---

### 3.2 Functional Core / Imperative Shell Separation

**Invariant**: Business logic must be in pure functions, I/O at boundaries.

**Why It Matters**: Pure functions are testable, predictable, and easier for AI to reason about.

**Check (grep for I/O in core)**:
```bash
# Find I/O operations in core business logic
grep -rn "open(\|requests\.\|boto3\|database\.\|print(" \
  src/core/ --include="*.py"

# Expected: No matches in core/
```

**Check (Custom Script)**:
```python
# invariant_checks/fcis_separation.py
import ast
import sys
from pathlib import Path

IO_FUNCTIONS = ['open', 'print', 'input']
IO_MODULES = ['requests', 'boto3', 'urllib', 'http', 'socket', 'os.system', 'subprocess']

def check_pure_module(filepath):
    with open(filepath) as f:
        tree = ast.parse(f.read())

    violations = []
    for node in ast.walk(tree):
        # Check function calls
        if isinstance(node, ast.Call):
            if isinstance(node.func, ast.Name):
                if node.func.id in IO_FUNCTIONS:
                    violations.append(f"Line {node.lineno}: I/O function '{node.func.id}'")

        # Check imports
        if isinstance(node, ast.Import):
            for alias in node.names:
                if any(io_mod in alias.name for io_mod in IO_MODULES):
                    violations.append(f"Line {node.lineno}: I/O import '{alias.name}'")
        if isinstance(node, ast.ImportFrom):
            if node.module and any(io_mod in node.module for io_mod in IO_MODULES):
                violations.append(f"Line {node.lineno}: I/O import from '{node.module}'")

    return violations

def main():
    all_violations = []
    for path in Path('src/core').rglob('*.py'):
        violations = check_pure_module(path)
        if violations:
            all_violations.append((path, violations))

    if all_violations:
        print("FCIS VIOLATIONS - I/O found in core modules:")
        for path, viols in all_violations:
            print(f"\n{path}:")
            for v in viols:
                print(f"  - {v}")
        sys.exit(1)
    print("PASS: Core modules are pure (no I/O)")
    sys.exit(0)

if __name__ == '__main__':
    main()
```

**How to Repair**:
1. Extract I/O operations to shell/boundary layer
2. Pass data to pure functions, return results
3. Shell handles all external interactions

**Agent Rules**:
```
When writing code in src/core/:
- NEVER use print(), open(), requests, boto3, or any I/O
- ALWAYS write pure functions: input → output with no side effects
- If you need I/O, put it in src/shell/ or src/adapters/
- Test: pure functions need no mocks, shell functions get integration tests
```

---

### 3.3 Service Factory Pattern Required

**Invariant**: All AWS/external service clients must come from factory.

**Why It Matters**: Factory pattern enables environment isolation, testing, and configuration consistency.

**Check (grep)**:
```bash
# Find direct client instantiation
grep -rn "boto3.client\|boto3.resource" src/ --include="*.py" | \
  grep -v "factory.py\|conftest.py\|test_"

# Expected: No matches outside factory
```

**Check (Python)**:
```python
# invariant_checks/service_factory_required.py
import re
import sys
from pathlib import Path

DIRECT_CLIENT_PATTERNS = [
    r'boto3\.client\s*\(',
    r'boto3\.resource\s*\(',
    r'=\s*DynamoDB\(',
    r'=\s*S3Client\(',
]

ALLOWED_FILES = ['factory.py', 'conftest.py']

def check_file(filepath):
    if any(allowed in str(filepath) for allowed in ALLOWED_FILES):
        return True, None
    if 'test_' in str(filepath):
        return True, None

    with open(filepath) as f:
        content = f.read()

    for pattern in DIRECT_CLIENT_PATTERNS:
        match = re.search(pattern, content)
        if match:
            return False, f"Direct client instantiation: {match.group()}"
    return True, None

def main():
    violations = []
    for path in Path('src').rglob('*.py'):
        ok, msg = check_file(path)
        if not ok:
            violations.append(f"{path}: {msg}")

    if violations:
        print("SERVICE FACTORY VIOLATIONS:")
        for v in violations:
            print(f"  - {v}")
        sys.exit(1)
    print("PASS: All clients come from factory")
    sys.exit(0)

if __name__ == '__main__':
    main()
```

**How to Repair**:
1. Import from factory: `from factory import get_dynamodb_client`
2. Remove direct boto3.client() calls
3. Use factory methods for all external services

**Agent Rules**:
```
When you need AWS clients:
- NEVER write boto3.client() or boto3.resource() directly
- ALWAYS use: `from factory import get_dynamodb_client, get_s3_client`
- Factory handles: environment detection, configuration, mocking for tests
- Exception: Factory implementation file itself
```

---

## 4. Security Invariants

### 4.1 No Hardcoded Secrets

**Invariant**: Secrets must come from environment or secrets manager, never hardcoded.

**Why It Matters**: Hardcoded secrets are exposed in version control and logs.

**Check (grep)**:
```bash
# Find potential hardcoded secrets
grep -rn "password\s*=\s*['\"].*['\"]\|api_key\s*=\s*['\"].*['\"]\|secret\s*=\s*['\"]" \
  src/ --include="*.py" | grep -v "test_\|example\|placeholder"

# Also check for AWS keys pattern
grep -rn "AKIA[A-Z0-9]{16}" src/ --include="*.py"

# Expected: No matches
```

**Check (detect-secrets)**:
```bash
# Using detect-secrets tool
pip install detect-secrets
detect-secrets scan src/ --all-files

# Or in CI pipeline
detect-secrets-hook --baseline .secrets.baseline
```

**Check (Custom Python)**:
```python
# invariant_checks/no_hardcoded_secrets.py
import re
import sys
from pathlib import Path

SECRET_PATTERNS = [
    (r'password\s*=\s*["\'][^"\']+["\']', 'hardcoded password'),
    (r'api_key\s*=\s*["\'][^"\']+["\']', 'hardcoded API key'),
    (r'secret\s*=\s*["\'][^"\']+["\']', 'hardcoded secret'),
    (r'AKIA[A-Z0-9]{16}', 'AWS access key'),
    (r'token\s*=\s*["\'][a-zA-Z0-9]{20,}["\']', 'hardcoded token'),
]

ALLOWED_VALUES = ['placeholder', 'example', 'test', 'mock', 'fake', '***', 'xxx']

def check_file(filepath):
    with open(filepath) as f:
        lines = f.readlines()

    violations = []
    for lineno, line in enumerate(lines, 1):
        for pattern, desc in SECRET_PATTERNS:
            if re.search(pattern, line, re.IGNORECASE):
                # Check if it's an allowed placeholder
                if not any(allowed in line.lower() for allowed in ALLOWED_VALUES):
                    violations.append(f"Line {lineno}: Potential {desc}")
    return violations

def main():
    all_violations = []
    for path in Path('src').rglob('*.py'):
        if 'test_' in str(path):
            continue
        violations = check_file(path)
        if violations:
            all_violations.append((path, violations))

    if all_violations:
        print("HARDCODED SECRETS DETECTED:")
        for path, viols in all_violations:
            print(f"\n{path}:")
            for v in viols:
                print(f"  - {v}")
        sys.exit(1)
    print("PASS: No hardcoded secrets found")
    sys.exit(0)

if __name__ == '__main__':
    main()
```

**How to Repair**:
1. Move secrets to environment variables
2. Use secrets manager (AWS Secrets Manager, HashiCorp Vault)
3. Reference via: `os.environ.get('API_KEY')`

**Agent Rules**:
```
When handling secrets:
- NEVER hardcode passwords, API keys, tokens, or connection strings
- ALWAYS use environment variables: os.environ.get('SECRET_NAME')
- For production: use secrets manager, reference by ARN
- In tests: use fake/mock values clearly labeled as such
- Before committing: run secret detection scan
```

---

### 4.2 Authentication Required on All Endpoints

**Invariant**: Every API endpoint must have authentication decorator/middleware.

**Why It Matters**: Unauthenticated endpoints are security vulnerabilities.

**Check (grep)**:
```bash
# Find route definitions without auth decorator
# This example is for Flask - adapt for your framework

# Find all routes
grep -rn "@app.route\|@blueprint.route" src/ --include="*.py" -A 2 | \
  grep -v "@require_auth\|@login_required"
```

**Check (Custom Script)**:
```python
# invariant_checks/auth_required.py
import ast
import sys
from pathlib import Path

ROUTE_DECORATORS = ['route', 'get', 'post', 'put', 'delete', 'patch']
AUTH_DECORATORS = ['require_auth', 'login_required', 'authenticated', 'jwt_required']
PUBLIC_ENDPOINTS = ['/health', '/healthz', '/ready', '/metrics', '/openapi']

def check_file(filepath):
    with open(filepath) as f:
        tree = ast.parse(f.read())

    violations = []

    for node in ast.walk(tree):
        if isinstance(node, ast.FunctionDef):
            decorators = []
            route_path = None

            for dec in node.decorator_list:
                # Get decorator name
                if isinstance(dec, ast.Call):
                    if isinstance(dec.func, ast.Attribute):
                        decorators.append(dec.func.attr)
                        # Extract route path
                        if dec.func.attr in ROUTE_DECORATORS and dec.args:
                            if isinstance(dec.args[0], ast.Constant):
                                route_path = dec.args[0].value
                    elif isinstance(dec.func, ast.Name):
                        decorators.append(dec.func.id)
                elif isinstance(dec, ast.Name):
                    decorators.append(dec.id)

            # Check if it's a route without auth
            is_route = any(d in ROUTE_DECORATORS for d in decorators)
            has_auth = any(d in AUTH_DECORATORS for d in decorators)
            is_public = route_path and any(pub in route_path for pub in PUBLIC_ENDPOINTS)

            if is_route and not has_auth and not is_public:
                violations.append(f"Function '{node.name}' at line {node.lineno}: route without auth")

    return violations

def main():
    all_violations = []
    for path in Path('src').rglob('*.py'):
        violations = check_file(path)
        if violations:
            all_violations.append((path, violations))

    if all_violations:
        print("AUTHENTICATION VIOLATIONS:")
        for path, viols in all_violations:
            print(f"\n{path}:")
            for v in viols:
                print(f"  - {v}")
        sys.exit(1)
    print("PASS: All endpoints have authentication")
    sys.exit(0)

if __name__ == '__main__':
    main()
```

**How to Repair**:
1. Add `@require_auth` decorator to endpoint
2. If endpoint should be public, add to PUBLIC_ENDPOINTS list with justification
3. Document why any endpoint is intentionally public

**Agent Rules**:
```
When creating API endpoints:
- ALWAYS add @require_auth decorator (or equivalent)
- Public endpoints (/health, /metrics) must be explicitly documented as public
- Never create unauthenticated endpoints without Human Orchestrator approval
- Check: every @route must have @require_auth above it
```

---

### 4.3 Input Validation Required

**Invariant**: All external inputs must be validated before use.

**Why It Matters**: Unvalidated input enables injection attacks, data corruption.

**Check (Static Analysis)**:
```bash
# Using bandit for Python
pip install bandit
bandit -r src/ -ll

# Check for SQL injection risks
bandit -r src/ --tests B608
```

**Check (Custom Script)**:
```python
# invariant_checks/input_validation.py
import ast
import sys
from pathlib import Path

VALIDATION_FUNCTIONS = ['validate', 'parse', 'sanitize', 'schema.load', 'Validator']
DANGEROUS_PATTERNS = ['eval', 'exec', 'compile', 'os.system', 'subprocess.call']

def check_handler_file(filepath):
    with open(filepath) as f:
        tree = ast.parse(f.read())

    violations = []

    for node in ast.walk(tree):
        if isinstance(node, ast.FunctionDef):
            # Check if function is a handler (has event/request parameter)
            is_handler = any(arg.arg in ['event', 'request', 'req']
                           for arg in node.args.args)

            if is_handler:
                # Check if validation is called
                has_validation = False
                for child in ast.walk(node):
                    if isinstance(child, ast.Call):
                        func_name = ''
                        if isinstance(child.func, ast.Name):
                            func_name = child.func.id
                        elif isinstance(child.func, ast.Attribute):
                            func_name = child.func.attr

                        if any(v in func_name.lower() for v in ['validate', 'parse', 'schema']):
                            has_validation = True
                            break

                if not has_validation:
                    violations.append(f"Handler '{node.name}' at line {node.lineno}: no input validation found")

    return violations

def main():
    all_violations = []
    for path in Path('src/handlers').rglob('*.py'):
        violations = check_handler_file(path)
        if violations:
            all_violations.append((path, violations))

    if all_violations:
        print("INPUT VALIDATION VIOLATIONS:")
        for path, viols in all_violations:
            print(f"\n{path}:")
            for v in viols:
                print(f"  - {v}")
        sys.exit(1)
    print("PASS: All handlers have input validation")
    sys.exit(0)

if __name__ == '__main__':
    main()
```

**How to Repair**:
1. Add schema validation (Pydantic, marshmallow, Zod)
2. Validate at entry point before processing
3. Return 400 errors for invalid input

**Agent Rules**:
```
When handling external input:
- ALWAYS validate input at the entry point (handler/controller)
- Use schema validation libraries (Pydantic, Zod, marshmallow)
- Never trust input: validate type, format, range, length
- Never use eval(), exec() with user input
- Log validation failures for security monitoring
```

---

## 5. API/Interface Invariants

### 5.1 API Versioning Required

**Invariant**: All API endpoints must include version prefix.

**Why It Matters**: Enables backward compatibility and safe API evolution.

**Check (grep)**:
```bash
# Find routes without version prefix
grep -rn "@.*route\s*(" src/ --include="*.py" | \
  grep -v "/v[0-9]\|/api/v[0-9]"
```

**Check (OpenAPI)**:
```bash
# Validate OpenAPI spec includes versioning
grep -q '"version"' openapi.yaml && grep -q '/v[0-9]' openapi.yaml
```

**How to Repair**:
1. Add version prefix: `/api/v1/users` instead of `/users`
2. Update OpenAPI spec with version info
3. Implement version negotiation if needed

**Agent Rules**:
```
When creating API endpoints:
- ALWAYS use version prefix: /api/v1/resource
- Update OpenAPI spec when adding/changing endpoints
- Consider backward compatibility before changing existing endpoints
- Breaking changes require new version: v1 → v2
```

---

### 5.2 Response Schema Consistency

**Invariant**: All API responses must follow standard envelope format.

**Why It Matters**: Consistent response format enables client code reuse and error handling.

**Check (pytest)**:
```python
# tests/invariants/test_response_schema.py
import pytest
from jsonschema import validate, ValidationError

RESPONSE_SCHEMA = {
    "type": "object",
    "required": ["status", "data"],
    "properties": {
        "status": {"type": "string", "enum": ["success", "error"]},
        "data": {"type": ["object", "array", "null"]},
        "error": {
            "type": "object",
            "properties": {
                "code": {"type": "string"},
                "message": {"type": "string"}
            }
        },
        "meta": {"type": "object"}
    }
}

def test_success_response_schema(client):
    """All success responses must match schema."""
    endpoints = ['/api/v1/users', '/api/v1/items']

    for endpoint in endpoints:
        response = client.get(endpoint)
        try:
            validate(response.json, RESPONSE_SCHEMA)
        except ValidationError as e:
            pytest.fail(f"{endpoint} response invalid: {e.message}")

def test_error_response_schema(client):
    """All error responses must match schema."""
    # Trigger a 404
    response = client.get('/api/v1/nonexistent')
    validate(response.json, RESPONSE_SCHEMA)
    assert response.json['status'] == 'error'
```

**How to Repair**:
1. Wrap all responses in standard envelope
2. Use response helper function consistently
3. Update error handlers to use envelope format

**Agent Rules**:
```
When returning API responses:
- ALWAYS use standard envelope: {"status": "success|error", "data": {...}}
- For errors: include "error": {"code": "...", "message": "..."}
- Use helper functions: return success_response(data) or error_response(code, msg)
- Never return raw data without envelope
```

---

## 6. Data Flow Invariants

### 6.1 No Production Data in Development

**Invariant**: Production data must never flow to development environments.

**Why It Matters**: Prevents data breaches, compliance violations, and accidental exposure.

**Check (grep for cross-env references)**:
```bash
# Find production references in dev code
grep -rn "prod\|production" src/ --include="*.py" | \
  grep -v "test_\|#.*prod"

# Check for production connection strings
grep -rn "prod\..*\.com\|production\..*\.amazonaws" src/
```

**Check (Environment Variable Audit)**:
```python
# invariant_checks/env_isolation.py
import os
import sys

def check_environment():
    """Ensure we're not mixing environments."""
    current_env = os.environ.get('ENVIRONMENT', 'development')

    violations = []

    # Check for production indicators in non-prod
    if current_env != 'production':
        prod_indicators = [
            'PROD_DB_HOST',
            'PRODUCTION_API_KEY',
            'PROD_BUCKET'
        ]
        for indicator in prod_indicators:
            if os.environ.get(indicator):
                violations.append(f"Production variable {indicator} found in {current_env}")

    if violations:
        print("ENVIRONMENT ISOLATION VIOLATIONS:")
        for v in violations:
            print(f"  - {v}")
        sys.exit(1)
    print(f"PASS: Environment isolation verified for {current_env}")
    sys.exit(0)

if __name__ == '__main__':
    check_environment()
```

**How to Repair**:
1. Use synthetic data in development
2. Implement data anonymization for testing
3. Separate credentials per environment
4. Use environment-specific configuration files

**Agent Rules**:
```
When working with data:
- NEVER copy production data to development
- ALWAYS use synthetic/mock data for testing
- Environment detection: check ENVIRONMENT variable
- Production operations require Trust Level 0-1 (human-controlled)
- If you need realistic test data, generate or anonymize it
```

---

### 6.2 Tenant Data Isolation

**Invariant**: Multi-tenant data must be isolated by tenant identifier.

**Why It Matters**: Prevents data leakage between tenants, required for compliance.

**Check (Database Query Audit)**:
```python
# invariant_checks/tenant_isolation.py
import ast
import sys
from pathlib import Path

def check_file(filepath):
    with open(filepath) as f:
        content = f.read()

    violations = []

    # Pattern: database queries without tenant_id
    if 'query(' in content or 'scan(' in content or 'get_item(' in content:
        if 'tenant_id' not in content and 'client_id' not in content:
            violations.append("Database operation may be missing tenant isolation")

    return violations

def main():
    all_violations = []
    for path in Path('src/services').rglob('*.py'):
        violations = check_file(path)
        if violations:
            all_violations.append((path, violations))

    if all_violations:
        print("TENANT ISOLATION WARNINGS:")
        for path, viols in all_violations:
            print(f"\n{path}:")
            for v in viols:
                print(f"  - {v}")
        # Warning, not failure - requires human review
        print("\nReview these files to ensure tenant isolation is implemented")
        sys.exit(0)
    print("PASS: No obvious tenant isolation issues")
    sys.exit(0)

if __name__ == '__main__':
    main()
```

**How to Repair**:
1. Add tenant_id to all data models
2. Include tenant_id in all queries
3. Use partition keys that include tenant identifier
4. Implement tenant context middleware

**Agent Rules**:
```
When working with multi-tenant data:
- ALWAYS include tenant_id in data models and queries
- Use composite keys: PK = TENANT#<tenant_id>, SK = <entity>#<id>
- Extract tenant from auth context, never from user input
- Test with multiple tenants to verify isolation
```

---

## 7. Test/Quality Invariants

### 7.1 Minimum Test Coverage

**Invariant**: Critical modules must maintain minimum test coverage.

**Why It Matters**: Ensures changes are validated, prevents regression.

**Check (pytest-cov)**:
```bash
# Run with coverage
pytest --cov=src --cov-report=term-missing --cov-fail-under=80

# For critical modules, higher threshold
pytest --cov=src/core --cov-fail-under=90
```

**Check (Coverage Script)**:
```python
# invariant_checks/coverage_threshold.py
import json
import sys

THRESHOLDS = {
    'src/core': 90,
    'src/services': 80,
    'src/handlers': 70,
}

def check_coverage():
    with open('coverage.json') as f:
        coverage_data = json.load(f)

    violations = []

    for module, min_coverage in THRESHOLDS.items():
        # Calculate coverage for module
        module_files = [f for f in coverage_data['files'] if f.startswith(module)]
        if module_files:
            total_lines = sum(coverage_data['files'][f]['summary']['num_statements']
                            for f in module_files)
            covered_lines = sum(coverage_data['files'][f]['summary']['covered_lines']
                              for f in module_files)
            coverage = (covered_lines / total_lines * 100) if total_lines > 0 else 0

            if coverage < min_coverage:
                violations.append(f"{module}: {coverage:.1f}% < {min_coverage}% required")

    if violations:
        print("COVERAGE VIOLATIONS:")
        for v in violations:
            print(f"  - {v}")
        sys.exit(1)
    print("PASS: Coverage thresholds met")
    sys.exit(0)

if __name__ == '__main__':
    check_coverage()
```

**How to Repair**:
1. Write tests for uncovered lines
2. Focus on branches and edge cases
3. Use mutation testing to find weak tests

**Agent Rules**:
```
When writing code:
- ALWAYS write tests for new functionality
- Maintain coverage: core ≥90%, services ≥80%, handlers ≥70%
- Run coverage check before committing
- If coverage drops, write tests before proceeding
```

---

### 7.2 Tests Must Assert Behavior

**Invariant**: Tests must have meaningful assertions, not just "no error".

**Why It Matters**: Tests without assertions pass regardless of actual behavior.

**Check (pytest plugin)**:
```python
# conftest.py - custom pytest hook
import pytest

def pytest_collection_modifyitems(items):
    """Flag tests without assertions."""
    for item in items:
        # Get test source
        source = inspect.getsource(item.function)

        # Check for assertions
        if 'assert' not in source and 'pytest.raises' not in source:
            item.add_marker(pytest.mark.xfail(reason="No assertions found"))
```

**Check (Static Analysis)**:
```python
# invariant_checks/test_assertions.py
import ast
import sys
from pathlib import Path

def check_test_file(filepath):
    with open(filepath) as f:
        tree = ast.parse(f.read())

    violations = []

    for node in ast.walk(tree):
        if isinstance(node, ast.FunctionDef) and node.name.startswith('test_'):
            has_assertion = False
            for child in ast.walk(node):
                if isinstance(child, ast.Assert):
                    has_assertion = True
                    break
                if isinstance(child, ast.Call):
                    if isinstance(child.func, ast.Attribute):
                        if child.func.attr in ['assertEqual', 'assertTrue', 'assertRaises']:
                            has_assertion = True
                            break

            if not has_assertion:
                violations.append(f"Test '{node.name}' at line {node.lineno}: no assertions")

    return violations

def main():
    all_violations = []
    for path in Path('tests').rglob('test_*.py'):
        violations = check_test_file(path)
        if violations:
            all_violations.append((path, violations))

    if all_violations:
        print("ASSERTION VIOLATIONS - Tests without assertions:")
        for path, viols in all_violations:
            print(f"\n{path}:")
            for v in viols:
                print(f"  - {v}")
        sys.exit(1)
    print("PASS: All tests have assertions")
    sys.exit(0)

if __name__ == '__main__':
    main()
```

**How to Repair**:
1. Add specific assertions for expected behavior
2. Assert return values, state changes, side effects
3. Use pytest.raises for expected exceptions

**Agent Rules**:
```
When writing tests:
- EVERY test must have at least one assert statement
- Assert specific values, not just "no error"
- Test edge cases: empty, null, max values, boundaries
- If testing for exceptions, use pytest.raises with specific exception
- Never write tests that just call a function without checking results
```

---

## 8. Dependency Invariants

### 8.1 No Vulnerable Dependencies

**Invariant**: All dependencies must be free of known critical vulnerabilities.

**Why It Matters**: Vulnerable dependencies are attack vectors.

**Check (pip-audit / npm audit)**:
```bash
# Python
pip install pip-audit
pip-audit --strict

# JavaScript
npm audit --audit-level=high

# Or in CI
pip-audit --require-hashes --strict || exit 1
```

**Check (safety)**:
```bash
pip install safety
safety check --full-report
```

**How to Repair**:
1. Update vulnerable package: `pip install package==safe_version`
2. If no fix available, find alternative package
3. If must use, document risk and mitigations

**Agent Rules**:
```
When managing dependencies:
- Run vulnerability scan before adding new dependencies
- Update vulnerable packages immediately
- Pin versions in requirements.txt/package.json
- Before committing: run pip-audit or npm audit
- If vulnerability found, fix before proceeding
```

---

### 8.2 License Compliance

**Invariant**: All dependencies must have compatible licenses.

**Why It Matters**: License violations can require code disclosure or legal action.

**Check (pip-licenses / license-checker)**:
```bash
# Python
pip install pip-licenses
pip-licenses --fail-on="GPL;AGPL;SSPL"

# JavaScript
npx license-checker --failOn "GPL;AGPL"
```

**How to Repair**:
1. Replace incompatible-licensed packages
2. If required, consult legal for license compatibility
3. Document any approved exceptions

**Agent Rules**:
```
When adding dependencies:
- Check license compatibility before adding
- Forbidden licenses: GPL, AGPL, SSPL (unless project is also GPL)
- Preferred: MIT, Apache-2.0, BSD
- Run license check: pip-licenses or npx license-checker
- If unclear, ask Human Orchestrator
```

---

## 9. Running Invariant Checks

### 9.1 Local Development

```bash
# Run all invariant checks
make check-invariants

# Or individually
python invariant_checks/no_direct_db_in_handlers.py
python invariant_checks/no_hardcoded_secrets.py
python invariant_checks/service_factory_required.py
```

### 9.2 CI/CD Integration

```yaml
# .github/workflows/invariants.yml
name: Invariant Checks

on: [push, pull_request]

jobs:
  invariants:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: pip install -r requirements-dev.txt

      - name: Code Structure Invariants
        run: |
          python invariant_checks/no_direct_db_in_handlers.py
          python invariant_checks/fcis_separation.py
          python invariant_checks/service_factory_required.py

      - name: Security Invariants
        run: |
          python invariant_checks/no_hardcoded_secrets.py
          python invariant_checks/auth_required.py
          bandit -r src/ -ll

      - name: Dependency Invariants
        run: |
          pip-audit --strict
          pip-licenses --fail-on="GPL;AGPL"

      - name: Test Quality Invariants
        run: |
          pytest --cov=src --cov-fail-under=80
          python invariant_checks/test_assertions.py
```

### 9.3 Pre-Commit Hooks

```yaml
# .pre-commit-config.yaml
repos:
  - repo: local
    hooks:
      - id: no-hardcoded-secrets
        name: Check for hardcoded secrets
        entry: python invariant_checks/no_hardcoded_secrets.py
        language: python
        pass_filenames: false

      - id: service-factory
        name: Check service factory usage
        entry: python invariant_checks/service_factory_required.py
        language: python
        pass_filenames: false
```

---

## 10. Agent Rules Summary

**Before Every Commit, AI Agents Must**:

```
Invariant Check Protocol:
1. Run all applicable invariant checks
2. If any check fails:
   - DO NOT commit
   - Fix the violation
   - Re-run checks
   - Only proceed when all pass
3. If unsure how to fix:
   - Ask Human Orchestrator for guidance
   - Document the issue
4. Never disable or skip invariant checks
```

**When Invariants Conflict with Task**:
```
If completing a task would require violating an invariant:
1. STOP - do not proceed with the violation
2. Report the conflict to Human Orchestrator
3. Wait for guidance:
   - Either: modify approach to satisfy invariant
   - Or: get explicit approval to update/waive invariant (rare)
4. Never silently violate invariants
```

---

## 10.5 Invariant Challenge Protocol

### Purpose

Unlike systems where AI must never question rules, AOSD allows AI agents to
challenge invariants when context makes them inappropriate.

This preserves:
- **Human authority**: Humans decide whether to allow exception, update invariant, or reject
- **AI insight**: AI surfaces friction that humans may not see
- **Adaptability**: Invariants evolve based on real-world feedback

> **Critical**: This protocol does NOT permit silent invariant violation under
> any circumstances. Challenges must be explicit and await human decision.
> While a challenge is pending, the AI must pause the affected task and take
> no action that would violate the invariant.

### When to Challenge

AI agents MAY challenge an invariant when:

| Situation | Example |
|-----------|---------|
| **Context mismatch** | Invariant written for web apps applied to CLI tool |
| **Conflicting invariants** | Two invariants contradict in this specific case |
| **Outdated assumption** | Invariant assumes constraint that no longer exists |
| **Harmful enforcement** | Enforcing would cause worse outcome with respect to system correctness, safety, compliance, or mission intent |

AI agents should NOT challenge because:
- The invariant is inconvenient
- Following it requires more work
- They prefer a different approach

### Grounding Requirement

> Challenges must be grounded in the *current task and observed behavior*,
> not hypothetical future scenarios.

Invalid challenges:
- "This might cause problems if we scale"
- "In some other context this could be wrong"
- "If we ever migrate to X, this won't apply"

These are design discussions, not invariant challenges. If an invariant
repeatedly causes friction across multiple contexts, escalate as a
Framework Stress Event instead.

### Challenge Structure

When challenging an invariant, AI must provide:

```
INVARIANT CHALLENGE

Invariant: [Name or description of invariant being challenged]
Level: [L1-Framework | L2-Profile | L3-Project]
Context: [Current task and environment]

Why it doesn't fit:
[Specific explanation of why this invariant is inappropriate HERE, NOW]

Evidence:
[Concrete examples, error messages, or observed behavior]

Proposed alternative:
[What the AI would do instead, and why it's better for this context]

Request: [EXCEPTION | UPDATE | CLARIFICATION]
```

### Human Response Options

| Response | Meaning | AI Action |
|----------|---------|-----------|
| **Allow Exception** | This case only, invariant stands | Proceed with alternative, document exception |
| **Update Invariant** | Invariant needs modification | Log as Lesson Learned for graduation |
| **Reject Challenge** | Invariant applies as written | Comply with invariant, find another approach |
| **Clarify** | Invariant is correct but unclear | Human provides interpretation |

The Human Orchestrator decides what level of approval is appropriate based on
the invariant's level and organizational context. The AI surfaces the level
for context; it does not enforce approval chains.

### Challenge vs Stress Event

| Signal | Action |
|--------|--------|
| Invariant causes friction *only in this context* | Issue a Challenge |
| Invariant repeatedly causes friction *across contexts* | Escalate as Framework Stress Event |

### Connection to Lessons Learned

Invariant challenges create feedback loops:

1. **Resolved or informative challenges logged** → Captured in LESSONS_LEARNED.md when they reveal actionable insight
2. **Repeated challenges to same invariant** → Signal invariant needs review
3. **Confirmed pattern** → Graduates to invariant update or documented exception policy

See [LESSONS_LEARNED.md](./LESSONS_LEARNED.md) for the graduation protocol.

### Guardrails

Challenges should be **infrequent and substantive**. If an AI frequently
challenges the same invariant, that's a signal the invariant needs review—
not that exceptions should become routine.

**Anti-patterns**:
- Challenging invariants to avoid work
- Repeated challenges without new information
- Challenging without proposing alternatives
- Treating one exception approval as blanket permission
- Speculative challenges about hypothetical scenarios

**If challenges become frequent**: The invariant itself needs attention. Open a
Framework Stress Event or update the invariant—don't normalize exceptions.

---

## 11. Deployment Authority Invariants

### 11.1 Deployable State Must Be Traceable to Main

**Invariant**: No deployment may occur unless the deployed commit is reachable from `origin/main`.

**Why It Matters**: When multiple agents or humans work concurrently, any ambiguity about what constitutes authoritative state will eventually cause work loss. A fix that exists only in a feature branch can be deployed, appear to work, and then be silently overwritten by a subsequent deployment from main. This isn't a multi-agent problem—it's an authority problem that multi-agent work surfaces faster.

**Check (git)**:
```bash
# Before any deployment, verify commit is on main
git fetch origin main
git merge-base --is-ancestor HEAD origin/main

# Exit code 0 = commit is ancestor of main (safe to deploy)
# Exit code 1 = commit is NOT on main (STOP - do not deploy)
```

**Check (Script)**:
```bash
#!/bin/bash
# invariant_checks/deploy_authority.sh

set -e

# Fetch latest main
git fetch origin main --quiet

# Check if current HEAD is reachable from origin/main
if git merge-base --is-ancestor HEAD origin/main; then
    echo "PASS: Current commit is on main branch"
    exit 0
else
    echo "DEPLOYMENT BLOCKED: Current commit is not reachable from origin/main"
    echo ""
    echo "Current HEAD: $(git rev-parse --short HEAD)"
    echo "Current branch: $(git branch --show-current)"
    echo ""
    echo "To deploy this work:"
    echo "  1. Merge your branch to main"
    echo "  2. Push to origin"
    echo "  3. Deploy from main"
    exit 1
fi
```

**Corollaries**:
- "Works locally" is irrelevant to deployment authority
- "Merged earlier" is irrelevant—only current branch state matters
- Feature branches are non-deployable by definition
- Hotfixes must flow through main before production deployment

**How to Repair** (when blocked):
1. Do NOT bypass the check
2. Merge your branch to main via normal PR process
3. Push main to origin
4. Re-run deployment from main

**Agent Rules**:
```
When deploying:
- ALWAYS verify: git merge-base --is-ancestor HEAD origin/main
- If check fails: STOP - do not proceed with deployment
- Do not deploy from feature branches, even if "it works"
- Deployment authority comes from main, not from test results
- Log blocked deployments for visibility
```

---

### 11.2 Deployment Provenance Required

**Invariant**: Every deployment must record provenance metadata.

**Why It Matters**: When issues arise, you need instant forensic clarity about what was deployed, from where, by whom, and when.

**Required Provenance Fields**:
```json
{
  "deployedCommit": "<full commit SHA>",
  "branch": "<branch name - should be main>",
  "pr": "<PR number that merged this work>",
  "agent": "<agent identifier or 'human'>",
  "timestamp": "<ISO 8601 timestamp>",
  "environment": "<target environment>"
}
```

**Check (Pre-Deploy)**:
```bash
# Verify provenance can be captured
COMMIT=$(git rev-parse HEAD)
BRANCH=$(git branch --show-current)
TIMESTAMP=$(date -u +"%Y-%m-%dT%H:%M:%SZ")

if [ -z "$COMMIT" ] || [ -z "$BRANCH" ]; then
    echo "FAIL: Cannot determine deployment provenance"
    exit 1
fi

if [ "$BRANCH" != "main" ]; then
    echo "WARNING: Deploying from non-main branch: $BRANCH"
fi

echo "Provenance captured:"
echo "  Commit: $COMMIT"
echo "  Branch: $BRANCH"
echo "  Timestamp: $TIMESTAMP"
```

**How to Repair**:
1. Add provenance capture to deployment pipeline
2. Store provenance in deployment logs/artifacts
3. Make provenance queryable for incident response

**Agent Rules**:
```
When deploying:
- ALWAYS capture and log provenance before deployment
- Required fields: commit, branch, timestamp, agent/operator
- Store provenance with deployment artifacts
- If provenance cannot be captured, deployment is non-compliant
```

---

### 11.3 Feature Branch Cleanup

**Invariant**: Feature branches must be deleted after merge to main.

**Why It Matters**: Branches that still exist after merge imply "maybe this is still authoritative." Agents and humans may infer deployability from branch presence. Deleting merged branches is an information-theoretic cleanup that removes authority ambiguity from the system state.

**Check (GitHub CLI)**:
```bash
# List merged branches that still exist
git fetch --prune origin
git branch -r --merged origin/main | grep -v 'main$' | grep -v 'HEAD'

# Expected: No matches (all merged branches deleted)
```

**Check (Automated)**:
```bash
#!/bin/bash
# invariant_checks/merged_branch_cleanup.sh

MERGED_BRANCHES=$(git branch -r --merged origin/main | grep -v 'main$' | grep -v 'HEAD' | wc -l)

if [ "$MERGED_BRANCHES" -gt 0 ]; then
    echo "WARNING: $MERGED_BRANCHES merged branches still exist"
    git branch -r --merged origin/main | grep -v 'main$' | grep -v 'HEAD'
    echo ""
    echo "Consider deleting these branches to reduce authority ambiguity"
    exit 0  # Warning, not failure
fi

echo "PASS: No stale merged branches"
exit 0
```

**How to Repair**:
1. Enable auto-delete on merge in GitHub/GitLab settings
2. Periodically clean up stale merged branches
3. Document any branches intentionally kept (release branches, etc.)

**Agent Rules**:
```
After merging PRs:
- Verify branch deletion occurred (or is configured to auto-delete)
- Do not reference merged feature branches as authoritative
- If branch still exists after merge, it is NOT a deployment source
- Only main represents deployable truth
```

---

## 12. Related Documents

- [AOSD Framework](./AOSD_FRAMEWORK.md) - Principle 4 (Architectural Invariants), Pattern 8 (IaC Invariant Enforcement)
- [Model Governance](./MODEL_GOVERNANCE.md) - Model quality fitness functions and regression detection
- [Anti-Patterns](./ANTI_PATTERNS.md) - What happens when invariants are violated
- [Development Workflow](./DEVELOPMENT_WORKFLOW.md) - How invariant checks fit into workflow
- [Testing Strategy Module](../SYLLABUS/MODULE_03_TESTING_AND_TDD_WITH_AI.md) - Testing in AOSD

---

**End of INVARIANTS_COOKBOOK.md**
