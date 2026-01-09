# Security Monitoring Reference Implementation

**AOSD Level 2 Example - AI-Assisted Security Monitoring**

> **Note**: This is a reference implementation showing how AOSD Goal 2 (Trustworthy-by-Design) and Goal 5 (AI Runtime Integration) can work together for security monitoring. Adapt these patterns for your specific security requirements and regulatory context.

---

## 1. Purpose

This document demonstrates how to integrate AI-assisted security monitoring into applications, providing:

- Runtime threat detection
- Anomaly identification
- Automated alerting with human escalation
- Audit trail integration
- Security incident response support

---

## 2. Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    APPLICATION LAYER                         │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  Security Event Emitters                             │   │
│  │  - Authentication events                             │   │
│  │  - Authorization checks                              │   │
│  │  - Data access events                                │   │
│  │  - Configuration changes                             │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                SECURITY MONITORING SERVICE                   │
│  ┌─────────────┐  ┌──────────────┐  ┌───────────────────┐  │
│  │ Event       │  │ Pattern      │  │ AI Analysis       │  │
│  │ Collector   │→ │ Matcher      │→ │ Engine            │  │
│  └─────────────┘  └──────────────┘  └───────────────────┘  │
│                                              │               │
│                                              ▼               │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                   RESPONSE ENGINE                    │   │
│  │  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌────────┐ │   │
│  │  │ Log     │  │ Alert   │  │ Block   │  │ Human  │ │   │
│  │  │ Only    │  │ Team    │  │ Action  │  │ Review │ │   │
│  │  └─────────┘  └─────────┘  └─────────┘  └────────┘ │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      AUDIT TRAIL                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ Immutable log of all security events and responses   │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

---

## 3. Pattern: Security Event Collection

### 3.1 Event Types

Define the security events your application emits:

```python
# models/security_events.py

class SecurityEventType(Enum):
    # Authentication
    LOGIN_SUCCESS = "auth.login.success"
    LOGIN_FAILURE = "auth.login.failure"
    LOGOUT = "auth.logout"
    PASSWORD_CHANGE = "auth.password.change"
    MFA_ENABLED = "auth.mfa.enabled"
    MFA_DISABLED = "auth.mfa.disabled"

    # Authorization
    ACCESS_GRANTED = "authz.access.granted"
    ACCESS_DENIED = "authz.access.denied"
    PERMISSION_ESCALATION = "authz.permission.escalation"

    # Data Access
    SENSITIVE_DATA_READ = "data.sensitive.read"
    SENSITIVE_DATA_EXPORT = "data.sensitive.export"
    BULK_DATA_ACCESS = "data.bulk.access"

    # Configuration
    CONFIG_CHANGE = "config.change"
    SECURITY_SETTING_CHANGE = "config.security.change"
    API_KEY_CREATED = "config.apikey.created"
    API_KEY_REVOKED = "config.apikey.revoked"

    # Anomalies
    UNUSUAL_LOCATION = "anomaly.location"
    UNUSUAL_TIME = "anomaly.time"
    UNUSUAL_PATTERN = "anomaly.pattern"

class SecurityEvent:
    id: str
    timestamp: datetime
    event_type: SecurityEventType
    user_id: Optional[str]
    session_id: Optional[str]
    ip_address: str
    user_agent: str
    resource: str
    action: str
    outcome: str  # success, failure, blocked
    metadata: Dict[str, Any]
    risk_score: Optional[float]  # 0.0 to 1.0, populated by AI
```

### 3.2 Event Emission

```python
# services/security_emitter.py

class SecurityEventEmitter:
    def __init__(self, event_queue, context_enricher):
        self.queue = event_queue
        self.enricher = context_enricher

    async def emit(self, event_type: SecurityEventType, **kwargs) -> SecurityEvent:
        """Emit a security event for monitoring."""
        event = SecurityEvent(
            id=generate_uuid(),
            timestamp=datetime.utcnow(),
            event_type=event_type,
            user_id=kwargs.get('user_id'),
            session_id=kwargs.get('session_id'),
            ip_address=self.enricher.get_ip(),
            user_agent=self.enricher.get_user_agent(),
            resource=kwargs.get('resource'),
            action=kwargs.get('action'),
            outcome=kwargs.get('outcome', 'success'),
            metadata=kwargs.get('metadata', {}),
        )

        # Enrich with context
        event = await self.enricher.enrich(event)

        # Queue for processing
        await self.queue.put(event)

        return event

# Usage in application code
class AuthenticationService:
    async def login(self, email: str, password: str) -> LoginResult:
        user = await self.user_repo.get_by_email(email)

        if not user or not self.verify_password(password, user.password_hash):
            await self.security.emit(
                SecurityEventType.LOGIN_FAILURE,
                user_id=user.id if user else None,
                resource="auth/login",
                action="authenticate",
                outcome="failure",
                metadata={"reason": "invalid_credentials"}
            )
            return LoginResult.failure("Invalid credentials")

        await self.security.emit(
            SecurityEventType.LOGIN_SUCCESS,
            user_id=user.id,
            resource="auth/login",
            action="authenticate",
            outcome="success"
        )
        return LoginResult.success(user)
```

---

## 4. Pattern: Rule-Based Detection

### 4.1 Detection Rules

```yaml
# config/security_rules.yaml
rules:
  - id: "brute_force_login"
    name: "Brute Force Login Attempt"
    description: "Multiple failed logins from same IP"
    event_types:
      - "auth.login.failure"
    conditions:
      count_threshold: 5
      time_window_seconds: 300
      group_by: "ip_address"
    severity: "high"
    response:
      - action: "alert"
        channel: "security_team"
      - action: "block_ip"
        duration_minutes: 15

  - id: "unusual_data_export"
    name: "Unusual Data Export"
    description: "Large data export outside normal hours"
    event_types:
      - "data.sensitive.export"
    conditions:
      time_outside: "09:00-18:00"
      size_threshold_mb: 100
    severity: "medium"
    response:
      - action: "alert"
        channel: "security_team"
      - action: "require_approval"
        timeout_minutes: 30

  - id: "permission_escalation"
    name: "Permission Escalation"
    description: "User granted elevated permissions"
    event_types:
      - "authz.permission.escalation"
    conditions:
      always_trigger: true
    severity: "medium"
    response:
      - action: "log"
        level: "audit"
      - action: "alert"
        channel: "admin_team"

  - id: "config_security_change"
    name: "Security Configuration Change"
    description: "Security-related configuration modified"
    event_types:
      - "config.security.change"
    conditions:
      always_trigger: true
    severity: "high"
    response:
      - action: "log"
        level: "audit"
      - action: "alert"
        channel: "security_team"
      - action: "notify_stakeholders"
```

### 4.2 Rule Engine

```python
# services/rule_engine.py

class SecurityRuleEngine:
    def __init__(self, rules_config, event_store, response_service):
        self.rules = self.load_rules(rules_config)
        self.event_store = event_store
        self.response = response_service

    async def evaluate(self, event: SecurityEvent) -> List[RuleMatch]:
        """Evaluate an event against all applicable rules."""
        matches = []

        for rule in self.rules:
            if event.event_type.value not in rule.event_types:
                continue

            if await self.check_conditions(event, rule):
                match = RuleMatch(
                    rule_id=rule.id,
                    event=event,
                    severity=rule.severity,
                    matched_at=datetime.utcnow()
                )
                matches.append(match)

                # Execute response actions
                for action in rule.response:
                    await self.response.execute(action, event, rule)

        return matches

    async def check_conditions(self, event: SecurityEvent, rule: Rule) -> bool:
        """Check if event matches rule conditions."""
        conditions = rule.conditions

        if conditions.get('always_trigger'):
            return True

        # Count-based conditions (e.g., brute force)
        if 'count_threshold' in conditions:
            count = await self.event_store.count_recent(
                event_type=event.event_type,
                group_by=conditions['group_by'],
                group_value=getattr(event, conditions['group_by']),
                window_seconds=conditions['time_window_seconds']
            )
            if count < conditions['count_threshold']:
                return False

        # Time-based conditions
        if 'time_outside' in conditions:
            normal_hours = conditions['time_outside']
            if self.is_within_hours(event.timestamp, normal_hours):
                return False

        # Size-based conditions
        if 'size_threshold_mb' in conditions:
            size_mb = event.metadata.get('size_bytes', 0) / (1024 * 1024)
            if size_mb < conditions['size_threshold_mb']:
                return False

        return True
```

---

## 5. Pattern: AI-Assisted Analysis

### 5.1 Anomaly Detection

```python
# services/ai_security_analyzer.py

class AISecurityAnalyzer:
    def __init__(self, ai_service, baseline_service):
        self.ai = ai_service
        self.baseline = baseline_service

    async def analyze_event(self, event: SecurityEvent) -> AnalysisResult:
        """Use AI to analyze security event for anomalies."""
        # Get user's baseline behavior
        user_baseline = await self.baseline.get_user_baseline(event.user_id)

        # Prepare context for AI
        context = {
            "event": event.to_dict(),
            "user_baseline": user_baseline.to_dict(),
            "recent_events": await self.get_recent_context(event)
        }

        prompt = f"""
        Analyze this security event for potential threats or anomalies.

        Event:
        - Type: {event.event_type.value}
        - User: {event.user_id}
        - Time: {event.timestamp}
        - IP: {event.ip_address}
        - Resource: {event.resource}
        - Outcome: {event.outcome}

        User's normal behavior:
        - Typical login times: {user_baseline.typical_login_hours}
        - Typical locations: {user_baseline.typical_locations}
        - Typical actions: {user_baseline.typical_actions}

        Recent activity:
        {self.format_recent_events(context['recent_events'])}

        Assess:
        1. Risk score (0.0 = normal, 1.0 = definite threat)
        2. Anomaly indicators (list any deviations from baseline)
        3. Recommended action (log_only, alert, investigate, block)
        4. Confidence level (low, medium, high)

        Respond in JSON format.
        """

        try:
            response = await self.ai.generate(prompt, max_tokens=300)
            analysis = self.parse_analysis(response)

            return AnalysisResult(
                event_id=event.id,
                risk_score=analysis['risk_score'],
                anomalies=analysis['anomaly_indicators'],
                recommended_action=analysis['recommended_action'],
                confidence=analysis['confidence'],
                ai_reasoning=response.text
            )
        except AIServiceError:
            # Fallback to rule-based only
            return AnalysisResult(
                event_id=event.id,
                risk_score=None,
                anomalies=[],
                recommended_action="log_only",
                confidence="low",
                ai_reasoning="AI analysis unavailable"
            )

    async def correlate_events(self, events: List[SecurityEvent]) -> CorrelationResult:
        """Analyze multiple events for attack patterns."""
        if len(events) < 2:
            return CorrelationResult(is_correlated=False)

        prompt = f"""
        Analyze these security events for potential coordinated attack patterns.

        Events (chronological):
        {self.format_events_for_correlation(events)}

        Determine:
        1. Are these events likely part of a coordinated attack?
        2. What attack pattern does this match (if any)?
        3. What is the likely attacker objective?
        4. Recommended response

        Consider: reconnaissance, credential stuffing, privilege escalation,
        data exfiltration, lateral movement.

        Respond in JSON format.
        """

        try:
            response = await self.ai.generate(prompt, max_tokens=400)
            return self.parse_correlation(response)
        except AIServiceError:
            return CorrelationResult(
                is_correlated=False,
                ai_reasoning="Correlation analysis unavailable"
            )
```

### 5.2 Baseline Learning

```python
# services/baseline_service.py

class UserBaselineService:
    def __init__(self, event_store, ai_service):
        self.event_store = event_store
        self.ai = ai_service

    async def build_baseline(self, user_id: str) -> UserBaseline:
        """Build behavioral baseline for a user."""
        # Get historical events (e.g., last 30 days)
        events = await self.event_store.get_user_events(
            user_id=user_id,
            since=datetime.utcnow() - timedelta(days=30)
        )

        baseline = UserBaseline(
            user_id=user_id,
            typical_login_hours=self.calculate_typical_hours(events),
            typical_locations=self.calculate_typical_locations(events),
            typical_actions=self.calculate_typical_actions(events),
            typical_resources=self.calculate_typical_resources(events),
            typical_session_duration=self.calculate_typical_duration(events),
            computed_at=datetime.utcnow()
        )

        return baseline

    def calculate_typical_hours(self, events: List[SecurityEvent]) -> str:
        """Determine user's typical activity hours."""
        login_events = [e for e in events if e.event_type == SecurityEventType.LOGIN_SUCCESS]
        if not login_events:
            return "unknown"

        hours = [e.timestamp.hour for e in login_events]
        # Find the most common 8-hour window
        # Simplified logic here
        avg_hour = sum(hours) / len(hours)
        return f"{int(avg_hour)-4:02d}:00-{int(avg_hour)+4:02d}:00"
```

---

## 6. Pattern: Human-in-the-Loop Escalation

### 6.1 Escalation Tiers

```
┌─────────────────────────────────────────────────────────────┐
│                    ESCALATION TIERS                          │
│                                                              │
│  TIER 1: Automated                                          │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ • Log event                                         │   │
│  │ • Update metrics                                    │   │
│  │ • Apply automatic blocks (if configured)            │   │
│  │ Risk: Low (0.0-0.3)                                  │   │
│  └─────────────────────────────────────────────────────┘   │
│                            │                                 │
│                            ▼                                 │
│  TIER 2: Team Alert                                         │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ • Alert security team via configured channel        │   │
│  │ • Create investigation ticket                        │   │
│  │ • Gather additional context automatically            │   │
│  │ Risk: Medium (0.3-0.7)                               │   │
│  └─────────────────────────────────────────────────────┘   │
│                            │                                 │
│                            ▼                                 │
│  TIER 3: Immediate Response                                 │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ • Page on-call security                              │   │
│  │ • Apply protective measures                          │   │
│  │ • Require human decision for reversal                │   │
│  │ Risk: High (0.7-1.0)                                 │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

### 6.2 Escalation Service

```python
# services/escalation_service.py

class EscalationService:
    def __init__(self, alert_channels, ticket_service, protection_service):
        self.channels = alert_channels
        self.tickets = ticket_service
        self.protection = protection_service

    async def escalate(
        self,
        event: SecurityEvent,
        analysis: AnalysisResult,
        rule_match: Optional[RuleMatch] = None
    ):
        """Escalate security incident based on severity."""
        tier = self.determine_tier(analysis.risk_score, rule_match)

        if tier >= 1:
            await self.tier1_response(event, analysis)

        if tier >= 2:
            await self.tier2_response(event, analysis, rule_match)

        if tier >= 3:
            await self.tier3_response(event, analysis, rule_match)

    def determine_tier(
        self,
        risk_score: Optional[float],
        rule_match: Optional[RuleMatch]
    ) -> int:
        """Determine escalation tier."""
        # Rule-based severity takes precedence
        if rule_match:
            if rule_match.severity == "critical":
                return 3
            elif rule_match.severity == "high":
                return 2
            elif rule_match.severity == "medium":
                return 1

        # Fall back to AI risk score
        if risk_score is not None:
            if risk_score >= 0.7:
                return 3
            elif risk_score >= 0.3:
                return 2
            else:
                return 1

        return 1  # Default to tier 1

    async def tier1_response(self, event: SecurityEvent, analysis: AnalysisResult):
        """Automated response - logging and metrics."""
        await self.log_security_event(event, analysis)
        await self.update_metrics(event, analysis)

    async def tier2_response(
        self,
        event: SecurityEvent,
        analysis: AnalysisResult,
        rule_match: Optional[RuleMatch]
    ):
        """Team alert response."""
        # Create alert
        alert = SecurityAlert(
            event_id=event.id,
            severity=rule_match.severity if rule_match else "medium",
            summary=self.generate_alert_summary(event, analysis),
            details=analysis.ai_reasoning,
            recommended_actions=analysis.recommended_action
        )

        # Send to appropriate channel
        await self.channels.send_alert("security_team", alert)

        # Create investigation ticket
        await self.tickets.create(
            type="security_investigation",
            severity=alert.severity,
            summary=alert.summary,
            context={
                "event": event.to_dict(),
                "analysis": analysis.to_dict()
            }
        )

    async def tier3_response(
        self,
        event: SecurityEvent,
        analysis: AnalysisResult,
        rule_match: Optional[RuleMatch]
    ):
        """Immediate response - page and protect."""
        # Page on-call
        await self.channels.page("security_oncall", {
            "severity": "critical",
            "event": event.to_dict(),
            "analysis": analysis.to_dict()
        })

        # Apply protective measures
        if analysis.recommended_action == "block":
            await self.protection.block_ip(
                event.ip_address,
                reason=f"Automated block: {analysis.anomalies}",
                duration_minutes=60,
                requires_human_unblock=True
            )

        if event.user_id and analysis.risk_score and analysis.risk_score > 0.9:
            await self.protection.suspend_user(
                event.user_id,
                reason="Automated suspension pending investigation",
                requires_human_reactivation=True
            )
```

### 6.3 Human Decision Interface

```python
# services/human_decision.py

class HumanDecisionService:
    """Interface for security decisions requiring human approval."""

    async def request_decision(
        self,
        decision_type: str,
        context: Dict,
        timeout_minutes: int = 30
    ) -> HumanDecision:
        """Request a human decision with timeout."""
        request = DecisionRequest(
            id=generate_uuid(),
            type=decision_type,
            context=context,
            requested_at=datetime.utcnow(),
            timeout_at=datetime.utcnow() + timedelta(minutes=timeout_minutes),
            status="pending"
        )

        await self.store_request(request)
        await self.notify_decision_makers(request)

        # Wait for decision or timeout
        decision = await self.wait_for_decision(request.id, timeout_minutes)

        if decision is None:
            # Timeout - apply default action
            return HumanDecision(
                request_id=request.id,
                decision="timeout_default",
                decided_by="system",
                decided_at=datetime.utcnow(),
                notes="No human decision within timeout"
            )

        return decision

    async def submit_decision(
        self,
        request_id: str,
        decision: str,
        decided_by: str,
        notes: str
    ) -> HumanDecision:
        """Submit a human decision."""
        request = await self.get_request(request_id)

        if request.status != "pending":
            raise DecisionAlreadyMade()

        human_decision = HumanDecision(
            request_id=request_id,
            decision=decision,
            decided_by=decided_by,
            decided_at=datetime.utcnow(),
            notes=notes
        )

        await self.store_decision(human_decision)
        await self.update_request_status(request_id, "decided")

        return human_decision
```

---

## 7. Pattern: Audit Trail

### 7.1 Audit Requirements

```python
# models/audit_trail.py

class AuditEntry:
    """Immutable audit trail entry."""
    id: str
    timestamp: datetime
    event_id: str  # Reference to security event
    event_type: str
    actor_id: Optional[str]  # User or system
    action: str
    resource: str
    outcome: str
    ip_address: str
    session_id: Optional[str]

    # Security monitoring additions
    risk_score: Optional[float]
    ai_analysis: Optional[str]
    rule_matches: List[str]
    escalation_tier: Optional[int]
    human_decisions: List[str]

    # Immutability
    checksum: str  # Hash of entry contents
    previous_checksum: str  # Chain integrity

class AuditTrailService:
    def __init__(self, audit_store, integrity_service):
        self.store = audit_store
        self.integrity = integrity_service

    async def record(
        self,
        event: SecurityEvent,
        analysis: Optional[AnalysisResult] = None,
        escalation: Optional[EscalationResult] = None
    ) -> AuditEntry:
        """Record immutable audit entry."""
        previous = await self.store.get_latest()
        previous_checksum = previous.checksum if previous else "genesis"

        entry = AuditEntry(
            id=generate_uuid(),
            timestamp=datetime.utcnow(),
            event_id=event.id,
            event_type=event.event_type.value,
            actor_id=event.user_id,
            action=event.action,
            resource=event.resource,
            outcome=event.outcome,
            ip_address=event.ip_address,
            session_id=event.session_id,
            risk_score=analysis.risk_score if analysis else None,
            ai_analysis=analysis.ai_reasoning if analysis else None,
            rule_matches=escalation.rule_matches if escalation else [],
            escalation_tier=escalation.tier if escalation else None,
            human_decisions=[],
            previous_checksum=previous_checksum,
            checksum=""  # Computed next
        )

        entry.checksum = self.integrity.compute_checksum(entry)

        await self.store.append(entry)

        return entry

    async def verify_integrity(self) -> IntegrityReport:
        """Verify audit trail has not been tampered with."""
        entries = await self.store.get_all()
        issues = []

        previous_checksum = "genesis"
        for entry in entries:
            # Verify chain
            if entry.previous_checksum != previous_checksum:
                issues.append(f"Chain break at {entry.id}")

            # Verify entry checksum
            computed = self.integrity.compute_checksum(entry)
            if entry.checksum != computed:
                issues.append(f"Checksum mismatch at {entry.id}")

            previous_checksum = entry.checksum

        return IntegrityReport(
            is_valid=len(issues) == 0,
            issues=issues,
            verified_at=datetime.utcnow(),
            entry_count=len(entries)
        )
```

---

## 8. Implementation Considerations

### 8.1 Performance

| Concern | Mitigation |
|---------|------------|
| Event volume | Async processing, event queuing |
| AI latency | Timeout with rule-based fallback |
| Storage growth | Retention policies, archival |
| Query performance | Indexed fields, time-based partitioning |

### 8.2 Reliability

| Concern | Mitigation |
|---------|------------|
| AI unavailable | Rule-based detection continues |
| Alert fatigue | Tunable thresholds, correlation |
| False positives | Human review, feedback loop |
| Missed events | Dead letter queue, replay capability |

### 8.3 Security of the Security System

| Concern | Mitigation |
|---------|------------|
| Attacker disables monitoring | Heartbeat monitoring, separate infrastructure |
| Log tampering | Immutable audit trail, checksums |
| Alert channel compromise | Multiple channels, out-of-band alerts |
| AI prompt injection | Input sanitization, output validation |

---

## 9. Testing Security Monitoring

### 9.1 Test Categories

```python
# tests/test_security_monitoring.py

class TestSecurityRuleEngine:
    def test_brute_force_detection(self):
        """Multiple failed logins should trigger brute force rule."""
        events = [
            create_login_failure(ip="1.2.3.4") for _ in range(5)
        ]
        for event in events:
            matches = rule_engine.evaluate(event)

        assert len(matches) == 1
        assert matches[0].rule_id == "brute_force_login"

    def test_below_threshold_no_trigger(self):
        """Failed logins below threshold should not trigger."""
        events = [
            create_login_failure(ip="1.2.3.4") for _ in range(3)
        ]
        for event in events:
            matches = rule_engine.evaluate(event)

        assert len(matches) == 0

class TestAIAnalyzer:
    async def test_anomaly_detection(self):
        """AI should detect deviation from baseline."""
        baseline = UserBaseline(
            typical_login_hours="09:00-17:00",
            typical_locations=["US"]
        )
        event = create_login_success(
            timestamp="03:00",
            location="RU"
        )

        analysis = await ai_analyzer.analyze_event(event, baseline)

        assert analysis.risk_score > 0.5
        assert "unusual_time" in analysis.anomalies
        assert "unusual_location" in analysis.anomalies

    async def test_fallback_on_ai_failure(self):
        """Should fallback gracefully when AI unavailable."""
        ai_service.disable()

        analysis = await ai_analyzer.analyze_event(event, baseline)

        assert analysis.recommended_action == "log_only"
        assert analysis.confidence == "low"

class TestEscalation:
    async def test_tier3_blocks_and_pages(self):
        """High risk should trigger tier 3 response."""
        analysis = AnalysisResult(risk_score=0.9, recommended_action="block")

        await escalation_service.escalate(event, analysis)

        assert protection_service.ip_blocked(event.ip_address)
        assert alert_channels.was_paged("security_oncall")
```

---

## 10. Related Documents

- [AOSD Framework - Goal 2](../../../AOSD_FRAMEWORK.md#goal-2--engineer-security-and-trustworthiness-from-the-start) - Trustworthy-by-Design
- [AOSD Framework - Goal 5](../../../AOSD_FRAMEWORK.md#goal-5--integrate-ai-into-applications-as-first-class-runtime-capabilities) - AI Runtime Integration
- [Troubleshooting - Integrated AI Features](../../../TROUBLESHOOTING.md#integrated-ai-features-in-applications) - Common issues
- [Integrated Training Example](./integrated-training-example.md) - Related integrated feature

---

**End of Security Monitoring Reference Implementation**
