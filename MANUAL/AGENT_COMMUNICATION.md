# Agent Communication & Protocols

**Secure Patterns for AI Agent Communication in AOSD**

---

## 1. Purpose

This document defines the **communication architecture and protocols** for AI agents operating within AOSD systems. It establishes:

- **Allowed communication patterns** between humans and agents
- **Prohibited patterns** that violate AOSD principles
- **Protocol recommendations** for implementation
- **Logging and auditability requirements**
- **MCP (Model Context Protocol) usage guidelines**

**Connection to AOSD Principles**:
- **Principle 1 (Multi-Agent Orchestration)**: Human Orchestrator directs all agents
- **Principle 2 (Trustworthy-by-Design)**: All communications must be auditable
- **Principle 8 (Human-in-the-Loop)**: Humans mediate agent interactions

---

## 2. Communication Architecture

### 2.1 Hub-and-Spoke Model

AOSD mandates a **hub-and-spoke communication architecture** where the Human Orchestrator serves as the central hub:

```
                         ┌─────────────────────┐
                         │  Human Orchestrator │
                         │       (Hub)         │
                         └─────────────────────┘
                                   │
                 ┌─────────────────┼─────────────────┐
                 │                 │                 │
                 ▼                 ▼                 ▼
          ┌──────────┐      ┌──────────┐      ┌──────────┐
          │ Builder  │      │ Reviewer │      │Specialist│
          │  Agent   │      │  Agent   │      │  Agent   │
          └──────────┘      └──────────┘      └──────────┘
              (Spoke)           (Spoke)           (Spoke)
```

**Key Properties**:
- All inter-agent communication flows through the Human Orchestrator
- Agents never communicate directly with each other
- The orchestrator maintains visibility into all information exchanges
- Decisions about what to share between agents rest with the human

### 2.2 Allowed Communication Patterns

| Pattern | Direction | Description | Example |
|---------|-----------|-------------|---------|
| **Human → Agent** | Orchestrator to AI | Instructions, specifications, context | "Implement this slice following the spec" |
| **Agent → Human** | AI to Orchestrator | Results, questions, recommendations | "I completed the task. Here's the PR" |
| **Human ← Agent → Human** | Mediated handoff | Orchestrator passes artifacts | Agent A's output reviewed, then passed to Agent B |
| **Agent → Artifact → Agent** | Async via artifacts | Agents share via committed code/docs | Builder commits, Reviewer reviews PR |

### 2.3 Prohibited Communication Patterns

| Pattern | Why Prohibited | Risk |
|---------|----------------|------|
| **Agent → Agent** (direct) | Bypasses human oversight | Emergent uncontrolled behavior |
| **Agent spawns Agent** | Unbounded resource usage | Cost explosion, loss of control |
| **Autonomous coordination** | No human arbitration | Conflicting actions, accountability loss |
| **Shared mutable state** | Race conditions | Data corruption, unpredictable state |
| **Direct tool invocation between agents** | Circumvents orchestration | Security boundary violations |

---

## 3. Protocol Recommendations

### 3.1 Synchronous Communication (Real-Time)

For real-time interaction between orchestrator and agents:

| Protocol | Use Case | Considerations |
|----------|----------|----------------|
| **REST/HTTP** | Standard API calls | Simple, stateless, well-understood |
| **WebSocket** | Streaming responses | Good for long-running AI generation |
| **gRPC** | High-performance, typed | Better for internal services, complex contracts |

**Implementation Guidance**:
```
# REST example: Orchestrator → Agent
POST /v1/agent/task
{
  "session_id": "sess_abc123",
  "task": "implement_slice",
  "spec": { ... },
  "context": { ... }
}

# Response: Agent → Orchestrator
{
  "session_id": "sess_abc123",
  "status": "completed",
  "result": { ... },
  "artifacts": [ ... ]
}
```

### 3.2 Asynchronous Communication (Queued)

For non-blocking, durable communication:

| Protocol | Use Case | Considerations |
|----------|----------|----------------|
| **Message Queues** (SQS, RabbitMQ) | Task distribution | Decoupling, retry handling |
| **Event Streams** (Kafka, EventBridge) | Event-driven workflows | Audit trail, replay capability |
| **Webhooks** | External integrations | Simple async notifications |

**When to Use Async**:
- Long-running agent tasks (> 30 seconds)
- Batch processing of multiple requests
- When durability and retry are critical
- Distributed multi-agent workflows

### 3.3 Artifact-Based Communication

Agents communicate indirectly through persistent artifacts:

| Artifact Type | Purpose | Visibility |
|---------------|---------|------------|
| **Git commits** | Code changes | Full history, diff-able |
| **Pull Requests** | Review handoffs | Comments, approvals tracked |
| **Documentation** | Context sharing | Versioned, searchable |
| **Handoff notes** | Session continuity | `.ai/memory/handoffs/` |
| **Coordination files** | Parallel work sync | `PARALLEL_DEV_COORDINATION.md` |

**Benefits**:
- Full audit trail
- Human-reviewable before sharing
- No direct coupling between agents
- Supports async workflows naturally

---

## 4. MCP (Model Context Protocol) Guidelines

### 4.1 MCP as Tool Layer, Not Communication Bus

**Critical Distinction**: MCP provides tools and resources to AI agents. It is **not** a communication channel between agents.

```
┌────────────────────────────────────────────────────────────┐
│                    Human Orchestrator                       │
│                                                            │
│   ┌──────────────┐    ┌──────────────┐                     │
│   │   Agent A    │    │   Agent B    │                     │
│   │              │    │              │                     │
│   │   ┌──────┐   │    │   ┌──────┐   │                     │
│   │   │ MCP  │   │    │   │ MCP  │   │                     │
│   │   │Client│   │    │   │Client│   │                     │
│   │   └──┬───┘   │    │   └──┬───┘   │                     │
│   └──────┼───────┘    └──────┼───────┘                     │
│          │                   │                             │
└──────────┼───────────────────┼─────────────────────────────┘
           │                   │
           ▼                   ▼
    ┌──────────────────────────────────┐
    │          MCP Server              │
    │  (Tools: file access, search,    │
    │   database, external APIs)       │
    └──────────────────────────────────┘
```

### 4.2 Allowed MCP Usage

| Usage | Allowed | Rationale |
|-------|---------|-----------|
| Agent reads files via MCP | ✅ Yes | Tool access to resources |
| Agent writes files via MCP | ✅ Yes | Tool execution |
| Agent searches codebase via MCP | ✅ Yes | Tool-assisted discovery |
| Agent calls external API via MCP | ✅ Yes | Controlled external access |
| Agent sends message to another agent via MCP | ❌ No | Violates hub-and-spoke |
| Agent triggers another agent via MCP | ❌ No | Autonomous spawning prohibited |
| Agents share state via MCP server | ❌ No | Shared mutable state prohibited |

### 4.3 MCP Security Considerations

**Tool Scoping**:
- Limit MCP tools to what each agent role needs
- Builder agents: file read/write, search, test execution
- Reviewer agents: file read, search, no write access
- Specialist agents: domain-specific tools only

**Resource Boundaries**:
- Scope MCP resources to working directory/worktree
- Never expose production resources to development agents
- Use separate MCP server configurations per environment

**Audit Requirements**:
- Log all MCP tool invocations
- Include: timestamp, agent, tool, parameters, result status
- Retain logs for compliance period

---

## 5. Logging and Auditability

### 5.1 Required Logging

Every agent communication must be logged with:

| Field | Description | Example |
|-------|-------------|---------|
| `timestamp` | ISO 8601 timestamp | `2025-01-15T14:30:00Z` |
| `session_id` | Unique session identifier | `sess_abc123` |
| `slice_id` | Associated slice (if applicable) | `SLICE-042` |
| `direction` | Communication direction | `human_to_agent`, `agent_to_human` |
| `agent_role` | Role of the agent | `builder`, `reviewer` |
| `action_type` | Type of communication | `task_assignment`, `result_submission` |
| `content_hash` | Hash of message content | `sha256:abc123...` |
| `status` | Outcome | `success`, `error`, `pending` |

### 5.2 Audit Log Schema

```json
{
  "version": "1.0",
  "timestamp": "2025-01-15T14:30:00Z",
  "session_id": "sess_abc123",
  "slice_id": "SLICE-042",
  "communication": {
    "direction": "human_to_agent",
    "agent_role": "builder",
    "action_type": "task_assignment",
    "content_hash": "sha256:abc123def456...",
    "content_summary": "Implement user authentication slice"
  },
  "metadata": {
    "orchestrator_id": "jim.wiedman",
    "model_id": "claude-3-5-sonnet-20241022",
    "token_count": {
      "input": 1500,
      "output": 0
    }
  },
  "status": "success"
}
```

### 5.3 Audit Trail Requirements

| Requirement | Implementation |
|-------------|----------------|
| **Immutability** | Append-only log storage |
| **Integrity** | Cryptographic hashing of entries |
| **Retention** | Minimum 90 days, longer for compliance |
| **Searchability** | Index by session, slice, agent, time |
| **Access Control** | Read access for auditors, no modification |

### 5.4 Traceability

All communications must be traceable to:

1. **Work Item**: Which issue/ticket initiated this work?
2. **Slice**: Which slice specification governs this work?
3. **Session**: Which agent session performed this work?
4. **Commit**: Which code changes resulted?
5. **Orchestrator**: Which human authorized this work?

---

## 6. Error Handling and Recovery

### 6.1 Communication Failures

| Failure Type | Detection | Recovery |
|--------------|-----------|----------|
| **Agent timeout** | No response within threshold | Retry with backoff, then escalate |
| **Malformed response** | Schema validation failure | Log error, retry once, then human review |
| **Context overflow** | Token limit exceeded | Summarize context, restart with reduced scope |
| **Tool failure** | MCP tool returns error | Log, retry if idempotent, escalate if persistent |

### 6.2 Circuit Breaker Pattern

Implement circuit breakers for agent communications:

```
States:
  CLOSED  → Normal operation, requests flow through
  OPEN    → Failures exceeded threshold, requests blocked
  HALF    → Testing recovery, limited requests

Thresholds:
  failure_count: 3        # Failures before opening
  reset_timeout: 60s      # Time before half-open
  success_count: 2        # Successes to close
```

### 6.3 Escalation Protocol

When automated recovery fails:

1. **Log** the failure with full context
2. **Alert** the Human Orchestrator
3. **Pause** the affected workflow
4. **Preserve** state for diagnosis
5. **Wait** for human decision to retry, abort, or modify

---

## 7. Implementation Checklist

### Pre-Deployment

- [ ] Communication architecture documented
- [ ] Allowed/prohibited patterns defined and enforced
- [ ] MCP servers scoped appropriately per role
- [ ] Audit logging implemented and tested
- [ ] Error handling and circuit breakers configured
- [ ] Escalation procedures documented

### Runtime Monitoring

- [ ] All communications logged
- [ ] No direct agent-to-agent communication detected
- [ ] MCP tool usage within expected bounds
- [ ] Error rates within thresholds
- [ ] Audit trail complete and searchable

### Periodic Review

- [ ] Audit logs reviewed for anomalies
- [ ] Communication patterns analyzed for drift
- [ ] Protocol efficiency assessed
- [ ] Security posture validated

---

## 8. Related Documents

- [Multi-Agent Patterns](./MULTI_AGENT_PATTERNS.md) - Safe multi-agent orchestration patterns with safety guardrails
- [Anti-Patterns](./ANTI_PATTERNS.md) - Section 2.3 covers peer-to-peer communication anti-pattern
- [AOSD Framework](./AOSD_FRAMEWORK.md) - Principles 1, 2, and 8 define orchestration and oversight requirements
- [Development Workflow](./DEVELOPMENT_WORKFLOW.md) - How communication fits into development phases
- [AI Prompting Patterns](./AI_PROMPTING_PATTERNS.md) - How to structure agent prompts

---

**End of AGENT_COMMUNICATION.md**
