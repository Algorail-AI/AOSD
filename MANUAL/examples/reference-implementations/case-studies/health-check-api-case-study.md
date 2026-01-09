# Case Study: Health Check API Implementation

**AOSD Level 2 Example - Narrative Case Study**

> **About This Case Study**: This narrative follows a team implementing a health check API using AOSD principles. It demonstrates how principles work together in practice, including handling unexpected complications.

---

## 1. Context

### The Situation

A development team is building a cloud-native application that needs comprehensive health monitoring. The product owner has requested a health check API that operations teams can use for monitoring and alerting.

### Initial Requirements

- Single endpoint returning system health status
- Check database connectivity
- Check external service dependencies
- Support for detailed vs. summary responses
- Must not expose sensitive information

### The Team

- **Human Orchestrator**: Sarah, senior developer
- **Builder Agent**: AI coding assistant (primary implementation)
- **Reviewer Agent**: AI assistant for code review (separate session)

---

## 2. Planning Phase

### Creating the Slice Specification

Sarah starts by creating a slice specification before any code is written. She uses the slice spec template to ensure all aspects are considered.

**Slice Spec Summary**:
```markdown
## Feature: Health Check API

### User Story
As an operations engineer, I want to query the system's health status
so that I can monitor availability and troubleshoot issues.

### Acceptance Criteria
1. GET /health returns 200 when system is healthy
2. GET /health returns 503 when any critical dependency is unhealthy
3. Response includes status of each checked component
4. Optional ?detail=true parameter provides verbose output
5. No credentials or sensitive data in response
6. Response time < 5 seconds

### Components Affected
- API layer: New endpoint
- Service layer: Health check coordinator
- Infrastructure: Database and external service connectivity tests

### Testing Requirements
- Fast: Unit tests for status aggregation logic
- Medium: Integration tests with mocked dependencies
- Slow: End-to-end test against real dependencies

### Security Considerations
- Rate limiting to prevent DoS
- No authentication required (intentional for monitoring)
- Sanitize any error messages
```

### TodoWrite Planning

Sarah then uses TodoWrite to break the slice into actionable tasks:

```
Todos:
1. [pending] Create health check endpoint structure
2. [pending] Implement database connectivity check
3. [pending] Implement external service checks
4. [pending] Add response aggregation logic
5. [pending] Implement detail parameter handling
6. [pending] Add rate limiting
7. [pending] Write fast tests for aggregation logic
8. [pending] Write medium tests with mocked services
9. [pending] Write slow end-to-end test
10. [pending] Code review and refinement
```

---

## 3. Implementation Journey

### Step 1: Creating the Endpoint Structure

Sarah marks the first task as in-progress and prompts the Builder Agent:

> "Create a health check endpoint at GET /health that returns a JSON response with overall status and individual component statuses. Follow the FCIS pattern - keep the endpoint handler thin and put logic in separate pure functions."

The Builder Agent produces:

```python
# handlers/health.py (Imperative Shell)
from services.health_checker import aggregate_health_status
from checks import database_check, service_check

async def health_endpoint(request):
    detail = request.query_params.get('detail', 'false') == 'true'

    checks = {
        'database': await database_check(),
        'external_api': await service_check('external-api'),
    }

    result = aggregate_health_status(checks, include_detail=detail)

    status_code = 200 if result['status'] == 'healthy' else 503
    return JSONResponse(result, status_code=status_code)
```

```python
# services/health_checker.py (Functional Core)
def aggregate_health_status(checks: dict, include_detail: bool = False) -> dict:
    """Pure function - aggregates individual check results."""
    all_healthy = all(c['healthy'] for c in checks.values())

    result = {
        'status': 'healthy' if all_healthy else 'unhealthy',
        'timestamp': get_current_timestamp(),
    }

    if include_detail:
        result['components'] = {
            name: {
                'status': 'healthy' if check['healthy'] else 'unhealthy',
                'latency_ms': check.get('latency_ms'),
            }
            for name, check in checks.items()
        }

    return result
```

Sarah marks task 1 complete and moves to task 2.

### Step 2: Database Connectivity Check

> "Implement the database connectivity check. It should verify connection and execute a simple query with timeout. Return healthy/unhealthy status with latency."

The Builder Agent produces the check:

```python
# checks/database.py
import asyncio
from db import get_connection

async def database_check(timeout_seconds: float = 3.0) -> dict:
    """Check database connectivity with timeout."""
    start = time.monotonic()
    try:
        async with asyncio.timeout(timeout_seconds):
            conn = await get_connection()
            await conn.execute("SELECT 1")
            latency = (time.monotonic() - start) * 1000
            return {'healthy': True, 'latency_ms': round(latency, 2)}
    except asyncio.TimeoutError:
        return {'healthy': False, 'error': 'timeout'}
    except Exception as e:
        return {'healthy': False, 'error': str(e)}
```

Sarah notices the error message might leak sensitive information (principle: Trustworthy-by-Design). She adds to her notes: "Need to sanitize error messages before final review."

Task 2 marked complete. Tasks 3-6 proceed similarly.

---

## 4. Complications

### Complication 1: External Service Check Timeout

During medium test implementation (task 8), Sarah discovers an issue:

**Symptom**: Tests intermittently fail when external service is slow.

**Investigation**: Sarah asks the Builder Agent to explain the timeout behavior. The agent reveals that the external service check doesn't have its own timeout - it relies on the HTTP client's default, which is 30 seconds.

**Resolution using AOSD principles**:

1. **TodoWrite Update**: Sarah adds a new task: "Add configurable timeout to external service check"
2. **Slice Adjustment**: Updates the slice spec to clarify timeout requirements
3. **Implementation**: Builder Agent adds timeout handling similar to database check
4. **Testing**: Adds specific test for timeout behavior

```python
# Updated checks/external_service.py
async def service_check(service_name: str, timeout_seconds: float = 5.0) -> dict:
    """Check external service with explicit timeout."""
    config = get_service_config(service_name)
    start = time.monotonic()
    try:
        async with asyncio.timeout(timeout_seconds):
            response = await http_client.get(config.health_url)
            latency = (time.monotonic() - start) * 1000
            return {
                'healthy': response.status_code == 200,
                'latency_ms': round(latency, 2)
            }
    except asyncio.TimeoutError:
        return {'healthy': False, 'error': 'timeout'}
    except Exception:
        return {'healthy': False, 'error': 'connection_failed'}
```

### Complication 2: Security Review Finding

During code review (task 10), the Reviewer Agent flags a security concern:

> "The error field in responses could leak implementation details. The database check returns `str(e)` which might include connection strings or internal paths."

**Resolution**:

Sarah implements error sanitization (addressing her earlier note):

```python
# services/error_sanitizer.py (Functional Core)
SAFE_ERROR_MESSAGES = {
    'timeout': 'Service timed out',
    'connection_failed': 'Service unavailable',
    'default': 'Check failed'
}

def sanitize_error(error: str) -> str:
    """Pure function - converts internal errors to safe external messages."""
    if 'timeout' in error.lower():
        return SAFE_ERROR_MESSAGES['timeout']
    if 'connection' in error.lower() or 'refused' in error.lower():
        return SAFE_ERROR_MESSAGES['connection_failed']
    return SAFE_ERROR_MESSAGES['default']
```

### Complication 3: Rate Limiting Scope Creep

When implementing rate limiting (task 6), the Builder Agent proposes a sophisticated system with per-IP tracking, sliding windows, and Redis-backed storage.

Sarah recognizes this as **over-engineering** (violating AOSD's simplicity principle). She redirects:

> "This health endpoint will only be called by our monitoring system, not exposed publicly. Implement simple in-memory rate limiting with a fixed window. We can enhance later if needed."

The final implementation is simpler:

```python
# middleware/rate_limit.py
from collections import defaultdict
import time

class SimpleRateLimiter:
    def __init__(self, max_requests: int = 60, window_seconds: int = 60):
        self.max_requests = max_requests
        self.window_seconds = window_seconds
        self.requests = defaultdict(list)

    def is_allowed(self, client_id: str) -> bool:
        now = time.time()
        window_start = now - self.window_seconds

        # Clean old requests
        self.requests[client_id] = [
            t for t in self.requests[client_id] if t > window_start
        ]

        if len(self.requests[client_id]) >= self.max_requests:
            return False

        self.requests[client_id].append(now)
        return True
```

---

## 5. Resolution

### Final Task Status

```
Todos:
1. [completed] Create health check endpoint structure
2. [completed] Implement database connectivity check
3. [completed] Implement external service checks
4. [completed] Add response aggregation logic
5. [completed] Implement detail parameter handling
6. [completed] Add rate limiting
7. [completed] Write fast tests for aggregation logic
8. [completed] Write medium tests with mocked services
9. [completed] Write slow end-to-end test
10. [completed] Code review and refinement
11. [completed] Add configurable timeout to external service check (added mid-implementation)
12. [completed] Sanitize error messages (added during review)
```

### Test Coverage

**Fast Tests** (run in <1s):
- `test_aggregate_all_healthy` - All checks pass
- `test_aggregate_one_unhealthy` - Single failure makes overall unhealthy
- `test_aggregate_with_detail` - Detail flag includes component info
- `test_aggregate_without_detail` - Summary only without flag
- `test_sanitize_timeout_error` - Timeout sanitized correctly
- `test_sanitize_connection_error` - Connection error sanitized
- `test_sanitize_unknown_error` - Unknown errors get generic message

**Medium Tests** (run in <10s):
- `test_health_endpoint_healthy` - Mocked healthy dependencies
- `test_health_endpoint_db_unhealthy` - Mocked database failure
- `test_health_endpoint_service_timeout` - Mocked timeout
- `test_rate_limit_allows_normal_traffic` - Under limit passes
- `test_rate_limit_blocks_excess` - Over limit returns 429

**Slow Tests** (run in <5min):
- `test_health_endpoint_real_dependencies` - Against real dev environment

### Final API Response

```json
// GET /health
{
  "status": "healthy",
  "timestamp": "2024-01-15T10:30:00Z"
}

// GET /health?detail=true
{
  "status": "healthy",
  "timestamp": "2024-01-15T10:30:00Z",
  "components": {
    "database": {
      "status": "healthy",
      "latency_ms": 12.5
    },
    "external_api": {
      "status": "healthy",
      "latency_ms": 45.2
    }
  }
}

// When unhealthy
{
  "status": "unhealthy",
  "timestamp": "2024-01-15T10:30:00Z",
  "components": {
    "database": {
      "status": "healthy",
      "latency_ms": 12.5
    },
    "external_api": {
      "status": "unhealthy",
      "error": "Service timed out"
    }
  }
}
```

---

## 6. Reflection: AOSD Principles in Action

### Principles Demonstrated

| Principle | How It Was Applied |
|-----------|-------------------|
| **Slice-Based Development** | Complete feature delivered end-to-end in one slice |
| **TodoWrite Workflow** | Tasks tracked, updated when complications arose |
| **FCIS Pattern** | Pure aggregation logic separated from I/O |
| **Testing Strategy** | Fast/Medium/Slow tiers with appropriate coverage |
| **Trustworthy-by-Design** | Security considered throughout, errors sanitized |
| **Human-in-the-Loop** | Sarah redirected over-engineering, made security decisions |
| **Multi-Agent Orchestration** | Builder implemented, Reviewer caught security issue |

### Key Decisions and Rationale

1. **Simple rate limiting over sophisticated**: Chose appropriate complexity for actual use case
2. **Error sanitization pattern**: Created reusable pure function for security
3. **Timeout configuration**: Made timeouts explicit rather than relying on defaults
4. **Detail parameter**: Allowed both summary and verbose output for different use cases

### What Could Have Gone Wrong Without AOSD

- **Without slice spec**: Might have implemented without considering security implications
- **Without TodoWrite**: Might have forgotten to add timeout handling discovered during testing
- **Without FCIS**: Testing aggregation logic would require mocking HTTP calls
- **Without review agent**: Security issue might have shipped to production
- **Without human oversight**: Over-engineered rate limiting would have added complexity

---

## 7. Lessons for Your Implementation

### Before Starting
1. Write a slice specification - it forces you to think through security and testing early
2. Use TodoWrite - it keeps you organized and lets you adapt when complications arise
3. Define your test tiers - know what you'll test at each level

### During Implementation
1. Follow FCIS - keep side effects at the boundaries
2. Note concerns for later - Sarah's note about error sanitization paid off
3. Resist over-engineering - simpler is usually better
4. Update TodoWrite as you learn - add tasks when you discover them

### During Review
1. Use a separate agent/session for review - fresh perspective catches issues
2. Focus on security - AI agents may optimize for functionality over safety
3. Check for information leakage - errors and logs often expose too much

---

## Related Documents

- [AOSD Framework](../../../AOSD_FRAMEWORK.md) - Core methodology
- [Slice Spec Template](../../../../TEMPLATES/SLICE_SPEC_TEMPLATE.md) - Feature specification template
- [Testing Strategy](../../../AOSD_FRAMEWORK.md#7-testing-strategy-fastmediumslow) - Test tier principles
- [Troubleshooting Guide](../../../TROUBLESHOOTING.md) - Common issues and solutions

---

**End of Case Study**
