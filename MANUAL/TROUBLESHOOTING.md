# AOSD Troubleshooting Guide

Practical solutions for common issues when using AOSD with AI agents.

**Format**: If you see X → Do Y

---

## AI Agent Issues

### AI Keeps Ignoring an Invariant

**Symptoms**:
- AI repeatedly violates the same architectural rule
- Code reviews consistently flag the same issue
- Pattern violations appear despite documentation

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Add invariant to prompt explicitly | AI may not have read or retained the invariant doc |
| Quote the exact rule in your request | Direct instruction takes precedence |
| Reduce scope of the task | Smaller tasks = fewer chances to drift |
| Add automated linting for the invariant | Catch violations before commit |
| Create a "DO NOT" section in prompts | Explicit prohibitions are harder to ignore |

**Example**:
```
CRITICAL INVARIANT: All service clients MUST be created via EnvironmentFactory.
DO NOT instantiate SDK clients directly.
```

---

### AI Produces Inconsistent Results

**Symptoms**:
- Same prompt yields different outputs
- Quality varies between sessions
- AI seems to "forget" previous context

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Break into smaller slices | Reduces cognitive load, improves consistency |
| Add more context to prompt | AI performs better with explicit context |
| Reference specific files/functions | Grounds the AI in actual code |
| Use TodoWrite to track progress | Maintains structured approach |
| Create a prompt template | Standardizes inputs for consistent outputs |

---

### AI Generates Code That Doesn't Compile

**Symptoms**:
- Import errors for non-existent modules
- Type errors for undefined properties
- Function calls with wrong signatures

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Check for hallucinated imports | AI may invent packages that don't exist |
| Verify dependencies in package.json/requirements.txt | Ensure imported packages are installed |
| Ask AI to verify APIs exist | "Before implementing, verify that X API exists" |
| Provide API documentation in context | Grounds AI in actual capabilities |
| Run type checker immediately after generation | Catch errors before investing more time |

**Quick Check**:
```bash
# TypeScript
npx tsc --noEmit

# Python
mypy your_file.py

# Go
go build ./...
```

---

### AI Removes Code Without Explanation

**Symptoms**:
- Tests fail after AI changes
- Features stop working
- Diff shows unexpected deletions

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Always review diffs before accepting | Catch deletions early |
| Ask AI to explain each deletion | Forces explicit justification |
| Add "preserve existing functionality" to prompt | Explicit instruction to maintain code |
| Use smaller, focused changes | Reduces accidental side effects |
| Require AI to list what it will remove before doing so | Preview before action |

**Prompt Addition**:
```
Before making changes, list any code you plan to remove and explain why.
Do not remove any existing functionality unless explicitly requested.
```

---

### AI Writes Insecure Code

**Symptoms**:
- Security scanner flags vulnerabilities
- Code review identifies OWASP issues
- Credentials or secrets in code

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Add security requirements to prompt | Explicit security expectations |
| Run SAST tools before commit | Automated vulnerability detection |
| Reference OWASP in prompts | Grounds AI in security standards |
| Review authentication/authorization code manually | High-risk areas need human eyes |
| Never commit until security scan passes | Gate on security validation |

**Security Prompt Addition**:
```
SECURITY REQUIREMENTS:
- No hardcoded credentials
- Parameterize all database queries
- Validate and sanitize all user input
- Use secure defaults
```

---

### AI Gets Stuck in a Loop

**Symptoms**:
- Same error after multiple attempts
- AI keeps proposing same wrong solution
- No progress despite iterations

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Start fresh with new context | Clears accumulated confusion |
| Rephrase the problem differently | New framing can unlock solutions |
| Break the problem into smaller parts | Isolate where AI is stuck |
| Provide a working example | Concrete reference beats abstract instruction |
| Try a different AI model or agent | Different models have different strengths |

**Decision Rule**:
```
If (same error × 3 iterations) → Restart with fresh context
If (partial progress each time) → Continue iterating
If (AI seems confused about requirements) → Rewrite specification
```

---

## Workflow Issues

### Tests Pass But Behavior Is Wrong

**Symptoms**:
- All green in CI but production bugs
- Edge cases cause failures
- User reports issues not caught by tests

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Add property-based tests | Discovers edge cases automatically |
| Review test assertions carefully | Tests may be too permissive |
| Add integration tests for user flows | Unit tests miss interaction bugs |
| Test with production-like data | Synthetic data may miss edge cases |
| Add negative test cases | Verify code handles invalid input |

**Test Review Checklist**:
- [ ] Tests check actual behavior, not just "no error"
- [ ] Edge cases covered (empty, null, boundary values)
- [ ] Error conditions tested
- [ ] Integration points validated

---

### Merge Conflicts Between AI Sessions

**Symptoms**:
- Multiple AI sessions modify same files
- Conflicts on merge to main
- Lost work from conflict resolution

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Use dedicated branch per AI session | Isolates changes |
| Keep slices smaller | Less overlap between changes |
| Coordinate which files each session touches | Avoid concurrent edits |
| Merge to main frequently | Reduces divergence |
| Use integration branch for related work | Coordinate before main |

**Branch Strategy**:
```
main
  └── feature/user-auth (Session 1)
  └── feature/user-profile (Session 2)
  └── integration/user-module (merge point)
```

---

### AI-Generated Code Is Hard to Debug

**Symptoms**:
- Don't understand what code does
- No logging or observability
- Can't trace execution flow

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Enforce observability patterns | Require logging in generated code |
| Ask AI to add debug logging | Explicit request for traceability |
| Ask AI to explain the code | Build understanding before debugging |
| Add correlation IDs to requests | Enable request tracing |
| Require comments for complex logic | Documentation aids understanding |

**Observability Prompt**:
```
Include structured logging for:
- Function entry/exit with parameters
- Decision points and branches taken
- External service calls with timing
- Error conditions with context
```

---

### Large Changes Are Hard to Review

**Symptoms**:
- Hundreds of lines changed
- Multiple unrelated modifications
- Review fatigue leads to missed issues

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Request smaller changes | Easier to review and understand |
| Ask AI to split into logical commits | Natural review boundaries |
| Review in sections, not all at once | Manage cognitive load |
| Use "safe mode" review process | Systematic verification |
| Ask AI to summarize each logical change | Provides review roadmap |

**Safe Mode Review**:
1. Read summary of intended changes
2. Verify each change matches intent
3. Check for unintended modifications
4. Test incrementally if possible
5. Get second review for large diffs (>200 lines)

---

### TodoWrite Tasks Get Out of Sync

**Symptoms**:
- Tasks marked complete that aren't done
- Missing tasks discovered mid-implementation
- Task list doesn't reflect actual progress

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Mark tasks complete immediately | Prevents drift |
| Add discovered tasks as you find them | Keeps list current |
| Review task list before each step | Maintains awareness |
| Only one task in-progress at a time | Clear focus |
| Break large tasks into subtasks | More granular tracking |

---

## Environment Issues

### Deployment Fails After AI Changes

**Symptoms**:
- Build succeeds locally, fails in CI/CD
- Deployment errors in staging/production
- Missing dependencies or configuration

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Verify bundling includes all dependencies | Serverless bundles often incomplete |
| Check environment-specific configuration | Dev settings may not transfer |
| Compare local vs CI environment | Identify environment differences |
| Run deployment dry-run before actual deploy | Catch issues early |
| Review infrastructure changes separately | IaC needs careful review |

**Deployment Checklist**:
- [ ] All dependencies in bundle
- [ ] Environment variables configured
- [ ] IAM/permissions correct
- [ ] Network/security groups allow access
- [ ] Configuration matches environment

---

### Security Scan Flags AI-Generated Code

**Symptoms**:
- SAST tools report vulnerabilities
- Dependency scanners find issues
- Security review fails

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Fix immediately—don't defer | Security issues compound over time |
| Review against OWASP Top 10 | Systematic security check |
| Ask AI to fix with security context | AI can remediate its own issues |
| Add security tests to prevent regression | Automated enforcement |
| Update AI prompts to prevent recurrence | Fix at the source |

**Common Security Fixes**:
| Issue | Fix |
|-------|-----|
| SQL Injection | Use parameterized queries |
| XSS | Sanitize output, use safe templates |
| Hardcoded secrets | Use environment variables or secret manager |
| Insecure deserialization | Validate input, use safe parsers |
| Missing authentication | Add auth middleware |

---

### Environment Configuration Drift

**Symptoms**:
- Works in dev, fails in staging/production
- Inconsistent behavior between environments
- "It works on my machine" syndrome

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Use infrastructure as code | Reproducible environments |
| Document all environment variables | Clear configuration requirements |
| Use Environment Factory pattern | Centralized configuration |
| Test in staging before production | Catch drift before it matters |
| Audit configuration differences regularly | Prevent drift accumulation |

---

### AI Changes Break Existing Functionality

**Symptoms**:
- Regression bugs after AI modifications
- Features that worked now fail
- Unintended side effects

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Run full test suite before committing | Catch regressions early |
| Add regression tests for fixed bugs | Prevent re-introduction |
| Review all changed files, not just intended ones | AI may modify unexpectedly |
| Use feature flags for risky changes | Gradual rollout reduces blast radius |
| Keep changes focused and minimal | Less change = less risk |

---

## Integrated AI Features in Applications

When AI is integrated as a runtime feature in your application (not just for development), additional failure modes emerge.

### AI Feature Returns Incorrect Results to Users

**Symptoms**:
- Users report wrong answers from AI features
- AI suggestions don't match context
- Generated content is inappropriate or off-topic

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Add input validation before AI calls | Prevents malformed requests |
| Implement output validation | Catches obviously wrong responses |
| Add confidence thresholds | Filter low-confidence results |
| Log all AI interactions | Enables debugging and pattern analysis |
| Provide user feedback mechanism | Users help identify issues |

**Validation Pattern**:
```python
def validate_ai_response(response, context):
    # Check for obviously wrong responses
    if response.confidence < 0.7:
        return fallback_response()
    if contains_inappropriate_content(response):
        log_and_alert("inappropriate_content", response)
        return fallback_response()
    if not relevant_to_context(response, context):
        return request_clarification()
    return response
```

---

### AI Feature Latency Spikes

**Symptoms**:
- User-facing AI features become slow
- Timeouts on AI-powered endpoints
- Inconsistent response times

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Implement request queuing | Prevents overload |
| Add circuit breakers | Fails fast when AI service is struggling |
| Cache common AI responses | Reduces API calls |
| Use streaming for long responses | Improves perceived performance |
| Set appropriate timeouts | Prevents hanging requests |

**Circuit Breaker Pattern**:
```
If (failure_rate > 50% in last 60 seconds):
    OPEN circuit → return cached/fallback response
Else if (circuit OPEN for > 30 seconds):
    HALF-OPEN → try one request
    If success: CLOSE circuit
    If failure: OPEN circuit
```

---

### AI Feature Costs Unexpectedly High

**Symptoms**:
- Token usage spikes
- API bills higher than expected
- Usage patterns don't match user activity

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Implement per-user rate limits | Prevents runaway usage |
| Add request size limits | Caps token consumption |
| Monitor token usage per feature | Identifies cost drivers |
| Cache responses aggressively | Reduces duplicate calls |
| Review prompt efficiency | Shorter prompts = lower cost |

**Cost Control Checklist**:
- [ ] Rate limits per user/session
- [ ] Maximum tokens per request
- [ ] Daily/monthly budget caps
- [ ] Alerting at 70% and 90% of budget
- [ ] Cost attribution per feature

---

### AI Feature Exposes Sensitive Data

**Symptoms**:
- AI responses contain user data from other users
- Internal system details leak in responses
- PII appears in AI-generated content

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Sanitize all inputs before AI calls | Prevents injection attacks |
| Filter outputs for sensitive patterns | Catches accidental leaks |
| Use separate AI contexts per user | Prevents cross-contamination |
| Audit AI training data | Ensure no sensitive data in models |
| Implement output review for sensitive domains | Human verification |

**Data Protection Pattern**:
```
Before AI call:
  1. Strip PII from context
  2. Replace real names with tokens
  3. Remove internal identifiers

After AI response:
  1. Scan for PII patterns (SSN, credit cards, etc.)
  2. Verify no leaked internal data
  3. Replace tokens back to safe display values
```

---

## Identifying AI Context Misunderstanding

AI agents may produce technically correct code that completely misses the point. This section helps identify and correct context misunderstanding.

### AI Solves the Wrong Problem

**Symptoms**:
- Code works but doesn't address the actual need
- Implementation misinterprets requirements
- Tests pass but feature doesn't serve users

**Diagnosis Questions**:
1. Did the prompt clearly state the **goal**, not just the **task**?
2. Was sufficient business context provided?
3. Did the AI have access to related existing code?
4. Were user stories or use cases included?

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Start prompts with "The goal is..." | Frames the purpose |
| Include user story context | Grounds in real needs |
| Show example of desired outcome | Concrete target |
| Ask AI to restate the problem first | Verify understanding |
| Provide "what success looks like" criteria | Clear finish line |

**Restatement Pattern**:
```
Before implementing, please:
1. Restate the problem you're solving
2. Describe who this helps and how
3. List your assumptions
4. Outline your approach

Wait for confirmation before proceeding.
```

---

### AI Misunderstands Existing Architecture

**Symptoms**:
- New code doesn't follow established patterns
- AI creates redundant functionality
- Integration points are wrong

**Diagnosis Questions**:
1. Did the AI read the relevant existing code?
2. Was architectural documentation provided?
3. Were related modules referenced in the prompt?
4. Did the prompt mention patterns to follow?

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Reference specific files: "Follow pattern in X.ts" | Direct guidance |
| Include invariants in every prompt | Consistent constraints |
| Ask AI to identify similar existing code first | Grounds in codebase |
| Provide architecture diagram or description | Big picture context |
| Show example of correct pattern | Concrete template |

**Architecture Context Template**:
```
ARCHITECTURE CONTEXT:
- This codebase uses [pattern name]
- Services are structured as [description]
- Key files to reference: [list]
- Follow the pattern established in [specific file]

INVARIANTS:
- [list critical rules]
```

---

### AI Misunderstands Domain Terminology

**Symptoms**:
- AI uses terms incorrectly
- Generated code reflects wrong domain model
- Naming doesn't match domain language

**Diagnosis Questions**:
1. Is domain terminology defined explicitly?
2. Did AI have access to glossary or domain docs?
3. Are there ambiguous terms that need clarification?

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Provide glossary in prompt | Clear definitions |
| Define terms before using them | No ambiguity |
| Use domain-specific examples | Contextual understanding |
| Ask AI to use specific terminology | Enforces consistency |
| Review domain model with AI first | Verify shared understanding |

**Domain Glossary Pattern**:
```
DOMAIN TERMS (use these exactly):
- "Customer": The end user who purchases products
- "Account": A customer's billing and profile record
- "Subscription": Recurring payment arrangement
- "Order": A single purchase transaction

Note: "User" in this codebase means admin user, not customer.
```

---

### AI Misunderstands Scope Boundaries

**Symptoms**:
- AI modifies files outside the intended scope
- Changes touch more than expected
- AI "improves" code that should be unchanged

**Diagnosis Questions**:
1. Was the scope explicitly bounded?
2. Were files to modify listed?
3. Did the prompt include "do not modify X"?

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| List files to modify explicitly | Clear boundaries |
| Add "ONLY modify these files" constraint | Explicit limitation |
| Specify what NOT to change | Negative constraints |
| Review diff for scope creep before accepting | Catch overreach |
| Use smaller, focused slices | Natural boundaries |

**Scope Boundary Template**:
```
SCOPE:
- Modify ONLY: src/features/auth/login.ts
- DO NOT modify: Any test files, any other features
- DO NOT refactor or "improve" unrelated code
- If you need to change other files, STOP and ask first
```

---

## Agent Behavioral Drift

Over time, AI agents can drift from expected behavior. This section covers detection and correction.

### Detecting Behavioral Drift

**Warning Signs**:

| Signal | Indicates |
|--------|-----------|
| Increasing invariant violations | Drift from architectural standards |
| More code review rejections | Quality degradation |
| Growing technical debt | Accumulated shortcuts |
| Longer iteration cycles | Declining efficiency |
| More security findings | Relaxing security practices |

**Monitoring Approach**:
```
Weekly drift check:
1. Count invariant violations (target: 0)
2. Measure first-pass code review approval rate
3. Track lines of code per feature (are they growing?)
4. Review security scan results trend
5. Check test coverage trend
```

---

### Causes of Agent Drift

| Cause | Description | Solution |
|-------|-------------|----------|
| Context accumulation | Long sessions accumulate confusing context | Fresh sessions for new features |
| Prompt degradation | Prompts become inconsistent over time | Maintain canonical prompt library |
| Codebase complexity | Growing complexity confuses AI | Regular architecture documentation updates |
| Outdated references | AI references deprecated patterns | Update AI context documents |
| Insufficient feedback | AI not corrected on mistakes | Consistent feedback loop |

---

### Correcting Agent Drift

**Immediate Corrections**:
1. **Restart with clean context** for current task
2. **Reference canonical patterns** explicitly
3. **Quote invariants** directly in prompt
4. **Reduce scope** of current task

**Systemic Corrections**:
1. **Audit prompt library** for inconsistencies
2. **Update architecture documentation** AI references
3. **Add automated checks** for common violations
4. **Schedule regular context resets**

**Drift Correction Prompt**:
```
RESET: Previous context may have accumulated errors.

CANONICAL PATTERNS:
[Quote exact patterns from documentation]

INVARIANTS:
[Quote exact invariants]

FOR THIS TASK:
Follow only the patterns and invariants above.
If unsure, ask rather than guess.
```

---

### Preventing Agent Drift

**Best Practices**:
| Practice | Frequency |
|----------|-----------|
| Fresh context for major features | Per feature |
| Prompt library audit | Weekly |
| Architecture doc sync | After major changes |
| Invariants review | Monthly |
| Full agent reset | When drift is detected |

**Anti-Drift Checklist** (run weekly):
- [ ] All prompts reference current invariants doc
- [ ] No deprecated patterns in prompt library
- [ ] AI context docs match actual architecture
- [ ] Recent code reviews show consistent quality
- [ ] Security scans show no new patterns of violation

---

## Reset vs. Iterate Decision Guide

Knowing when to continue iterating versus starting fresh is crucial for efficient development.

### Decision Tree

```
Start Here
    │
    ▼
Is the core approach fundamentally wrong?
    │
    ├── YES → RESET with different approach
    │
    ▼ NO
Have you iterated more than 3 times on the same issue?
    │
    ├── YES → Is there partial progress each iteration?
    │           │
    │           ├── YES → Continue with more specific constraints
    │           │
    │           └── NO → RESET with clearer specification
    │
    ▼ NO
Is the AI stuck in a pattern it can't break?
    │
    ├── YES → RESET with fresh context
    │
    ▼ NO
Are you spending more time fixing than would take to rewrite?
    │
    ├── YES → RESET
    │
    ▼ NO
CONTINUE ITERATING with incremental improvements
```

### Reset Indicators

**Strong signals to RESET**:
- Same error repeats after 3+ attempts with different approaches
- AI is clearly confused about the fundamental requirement
- Accumulated context is causing confusion (long session)
- Core architecture of the solution is wrong
- Fixing existing code would take longer than starting over

**RESET approach**:
1. Start new session/context
2. Rewrite the specification more clearly
3. Include explicit anti-patterns from failed attempts
4. Reference only necessary existing code
5. Validate understanding before implementation

### Iterate Indicators

**Strong signals to ITERATE**:
- Core solution is correct, just needs refinement
- Each iteration shows measurable progress
- Issues are isolated and well-understood
- Changes are small and low-risk
- AI demonstrates understanding of the problem

**ITERATE approach**:
1. Identify specific issue to address
2. Provide targeted feedback
3. Keep scope minimal
4. Test after each iteration
5. Document what works for future reference

### Time-Based Heuristics

| Situation | Action |
|-----------|--------|
| < 30 min on issue, making progress | Iterate |
| 30-60 min, partial progress | Try one more iteration with constraints |
| > 60 min, same issue | Reset |
| > 3 sessions on same problem | Reconsider approach entirely |

### Reset Prompt Template

```
FRESH START - Previous approach had issues.

WHAT DIDN'T WORK:
- [Describe failed approach 1]
- [Describe failed approach 2]

DO NOT:
- [Anti-patterns from previous attempts]

REQUIREMENTS (restated clearly):
- [Requirement 1]
- [Requirement 2]

APPROACH CONSTRAINTS:
- [Any new constraints based on learning]

Please propose an approach before implementing.
```

---

## Troubleshooting in Restrictive Environments

Special considerations for network-isolated or heavily regulated environments.

### Limited AI Connectivity

**Symptoms**:
- No direct AI API access from development environment
- Network-isolated systems
- Network restrictions block AI services

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Use AI for planning outside, execute inside | Separates thinking from doing |
| Prepare detailed specs externally | AI contributes via documentation |
| Use local/on-premise AI where approved | AI within the boundary |
| Develop thorough manual review processes | Compensates for reduced AI assistance |
| Create comprehensive checklists | Captures AI-like systematic thinking |

**Dual-Environment Workflow**:
```
UNRESTRICTED ENVIRONMENT:
  1. AI helps plan slice specifications
  2. AI generates code templates/patterns
  3. AI reviews approach documentation

TRANSFER (human reviews, sanitizes)

RESTRICTED ENVIRONMENT:
  4. Human adapts plan to restricted context
  5. Human implements with detailed spec
  6. Human performs manual review using checklists
```

---

### Reduced AI Capability

**Symptoms**:
- Only basic AI available (not latest models)
- Limited context windows
- No specialized coding AI

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Break tasks into smaller pieces | Fits limited context |
| Provide more explicit context | Compensates for less capable model |
| Use more detailed prompts | Reduces ambiguity |
| Rely more on human review | Compensates for AI limitations |
| Create stricter checklists | Systematic verification |

**Limited-AI Prompt Pattern**:
```
CONTEXT (be explicit, assume nothing):
- We are modifying [exact file path]
- This file does [exact purpose]
- Current code structure: [brief summary]

TASK (be very specific):
- Add [exact feature]
- Do not modify [explicit exclusions]

CONSTRAINTS:
- Follow this exact pattern: [show pattern]
- Maximum changes: [limit]
```

---

### Restricted Documentation Access

**Symptoms**:
- Can't share code with external AI
- Documentation can't leave secure environment
- No external reference access

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Create sanitized examples | Safe to share externally |
| Build internal pattern libraries | Reference without exposing secrets |
| Use generic problem descriptions | Get help without revealing specifics |
| Maintain internal documentation | Self-contained guidance |
| Train team on patterns | Human knowledge transfer |

**Sanitization Checklist**:
- [ ] No project names or identifiers
- [ ] No internal URLs or endpoints
- [ ] No real data samples
- [ ] No security implementation details
- [ ] No organizational structure info
- [ ] Generic variable/function names

---

### Audit and Compliance Tracking

**Symptoms**:
- Need to demonstrate AI-assisted vs. human-written code
- Compliance requires AI usage documentation
- Audit trails for AI contributions

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Tag commits with AI involvement level | Clear audit trail |
| Maintain AI usage logs | Demonstrates compliance |
| Document AI prompts used | Reproducibility |
| Track human review of AI output | Verification evidence |
| Separate AI planning from implementation | Clear boundaries |

**Commit Tagging Convention**:
```
git commit -m "[AI-PLANNED] Feature description

AI Involvement: Planning only
Implementation: Human
Review: Human per [checklist ID]
Compliance: [requirement ID]"
```

---

## Team Workflow Troubleshooting

Issues specific to multi-developer AOSD environments.

### Conflicting AI Sessions

**Symptoms**:
- Multiple developers' AI outputs conflict
- Merge conflicts in AI-generated code
- Inconsistent patterns across the codebase

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Coordinate slice ownership clearly | Avoid overlapping work |
| Use shared prompt templates | Consistent AI behavior |
| Merge frequently | Reduce conflict accumulation |
| Assign file ownership | Clear boundaries |
| Hold coordination standups | Align work |

**Slice Ownership Matrix**:
```
| Slice         | Owner  | Files              | Status    |
|---------------|--------|--------------------|-----------|
| User Auth     | Alice  | src/auth/*         | In Progress |
| Payments      | Bob    | src/payments/*     | In Progress |
| Notifications | Carol  | src/notifications/*| Planned   |
```

---

### Inconsistent AI Prompt Practices

**Symptoms**:
- Different developers get different AI behavior
- Code style varies by who wrote the prompt
- Some developers more effective with AI than others

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Create shared prompt library | Standardization |
| Document effective patterns | Knowledge sharing |
| Conduct prompt reviews | Quality control |
| Pair on AI sessions | Skill transfer |
| Track prompt effectiveness | Continuous improvement |

**Prompt Library Structure**:
```
prompts/
├── invariants/
│   └── base-invariants.md       # Always include
├── patterns/
│   ├── new-feature.md           # Feature development
│   ├── bug-fix.md               # Bug fixing
│   └── refactor.md              # Refactoring
└── reviews/
    └── code-review.md           # Reviewer prompts
```

---

### Knowledge Silos from AI Sessions

**Symptoms**:
- Only one person understands AI-generated code
- Handoffs are difficult
- Code becomes unmaintainable when author leaves

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Require documentation for AI-generated code | Captured knowledge |
| Use code review to spread understanding | Knowledge transfer |
| Pair on complex AI sessions | Shared context |
| Record key AI interactions | Decision trail |
| Rotate slice ownership periodically | Spreads familiarity |

**Knowledge Capture Template**:
```markdown
## AI Session Summary

**Feature**: [name]
**Developer**: [who]
**Date**: [when]

### What AI Generated
- [list major components]

### Key Decisions Made
- [decision 1]: [rationale]
- [decision 2]: [rationale]

### Non-Obvious Implementation Notes
- [note 1]
- [note 2]

### How to Modify This Code
- [guidance for future developers]
```

---

### Uneven AI Adoption Across Team

**Symptoms**:
- Some team members use AI heavily, others don't
- Velocity varies significantly by developer
- Resistance to AOSD practices

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Start with willing adopters | Build success stories |
| Pair skeptics with effective users | Show don't tell |
| Measure outcomes, not activity | Focus on results |
| Address concerns directly | Remove blockers |
| Provide training | Build skills |

**Adoption Progression**:
```
Level 0: No AI usage
Level 1: AI for code completion only
Level 2: AI for single-function generation
Level 3: AI for slice implementation with prompts
Level 4: Full AOSD workflow with TodoWrite

Move team members up one level at a time.
```

---

### Code Review Bottlenecks with AI

**Symptoms**:
- Too much AI-generated code to review
- Review quality declining
- PRs waiting too long

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Use AI reviewer for first pass | Reduces human load |
| Smaller, more frequent PRs | Manageable review size |
| Set PR size limits | Enforces small changes |
| Rotate reviewers | Distribute load |
| Fast-track low-risk changes | Prioritize effort |

**Review Triage Matrix**:
```
| Change Type          | AI Review | Human Review | Depth |
|----------------------|-----------|--------------|-------|
| New feature          | Yes       | Required     | Deep  |
| Bug fix              | Yes       | Required     | Medium|
| Test only            | Yes       | Optional     | Light |
| Documentation        | Yes       | Optional     | Light |
| Refactor (no behavior)| Yes      | Required     | Medium|
| Security-related     | Yes       | Required     | Deep  |
```

---

### Onboarding New Team Members to AOSD

**Symptoms**:
- New developers struggle with AOSD practices
- Inconsistent adoption from new hires
- Long ramp-up time for AI-assisted development

**Solutions**:
| Try This | Why It Works |
|----------|--------------|
| Provide AOSD onboarding checklist | Clear expectations |
| Assign an AOSD mentor | Personal guidance |
| Start with simple slices | Build confidence |
| Review first AI sessions together | Immediate feedback |
| Share effective prompt examples | Accelerate learning |

**Onboarding Checklist**:
- [ ] Read AOSD Framework overview
- [ ] Review project's invariants document
- [ ] Study prompt library examples
- [ ] Pair on first AI session with mentor
- [ ] Complete first slice with review
- [ ] Contribute to prompt library
- [ ] Lead a slice independently

---

## Quick Reference

### Before Accepting AI Output
- [ ] Reviewed diff for unexpected changes
- [ ] Verified no code removed unintentionally
- [ ] Checked for hallucinated imports/APIs
- [ ] Ran type checker/linter
- [ ] Ran test suite
- [ ] Verified observability patterns present

### When Something Goes Wrong
1. **Stop** - Don't compound the problem
2. **Revert** - Get to known good state
3. **Analyze** - Understand what went wrong
4. **Fix Process** - Prevent recurrence
5. **Retry** - With improved approach

### Escalation Path
1. Try solution from this guide
2. Break problem into smaller pieces
3. Ask AI to explain/debug
4. Fresh start with new context
5. Try different AI model/agent
6. Human expert review

---

## Emerging Patterns

For patterns not yet stable enough for this guide, see [LESSONS_LEARNED.md](./LESSONS_LEARNED.md). That document serves as working memory for operational learnings that may graduate here once validated.

---

## Related Documents

- [AOSD Framework](AOSD_FRAMEWORK.md) - Core methodology
- [Lessons Learned](./LESSONS_LEARNED.md) - Working memory for emerging patterns
- [Failure Modes & Recovery](AOSD_FRAMEWORK.md#-failure-modes--recovery) - Detailed failure patterns
- [Observability for AI-Generated Code](AOSD_FRAMEWORK.md#-observability-for-ai-generated-code) - Logging and debugging
- [Adapting AOSD to Your Environment](AOSD_FRAMEWORK.md#-adapting-aosd-to-your-environment) - Government, ITAR, and regulated contexts
- [Scaling AOSD for Teams](AOSD_FRAMEWORK.md#-scaling-aosd-for-teams-and-organizations) - Team coordination patterns
- [Integrating AI Into Applications](AOSD_FRAMEWORK.md#-integrating-ai-into-applications) - Runtime AI features

---

**End of TROUBLESHOOTING.md**
