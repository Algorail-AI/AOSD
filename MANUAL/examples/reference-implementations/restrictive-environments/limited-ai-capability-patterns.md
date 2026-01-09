# Limited AI Capability Patterns

**AOSD Level 2 Example - Working with Constrained AI Capabilities**

> **Note**: This is a reference implementation showing patterns for applying AOSD when AI capabilities are limited. These patterns help maintain AOSD principles even when AI agents have reduced reasoning, context windows, or multi-file capabilities.

---

## 1. Purpose

This document provides patterns for environments where AI capabilities are constrained:

- Smaller context windows
- Limited multi-file reasoning
- Reduced planning capability
- Slower response times
- No internet access for the model
- Older or smaller model versions

These patterns help you get maximum value from limited AI while maintaining AOSD principles.

---

## 2. Core Pattern: Planner + Executor Separation

### 2.1 The Problem

Full-capability AI agents can:
- Reason across multiple files
- Plan and execute in one session
- Maintain context over long conversations

Limited AI agents often cannot do these safely.

### 2.2 The Solution

Separate planning from execution explicitly:

```
┌─────────────────────────────────────────────────────────────┐
│                    PLANNING PHASE                            │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │
│  │ Human       │  │ AI Planner  │  │ Output:             │  │
│  │ Context     │→ │ (or Human)  │→ │ Atomic Task List    │  │
│  └─────────────┘  └─────────────┘  └─────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    EXECUTION PHASE                           │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │
│  │ Atomic Task │→ │ AI Executor │→ │ Single-File Change  │  │
│  └─────────────┘  └─────────────┘  └─────────────────────┘  │
│                                                              │
│  [Repeat for each atomic task]                              │
└─────────────────────────────────────────────────────────────┘
```

### 2.3 Planning Phase Output

The planner (AI or human) produces:

```yaml
task: "Add input validation to user registration"

atomic_steps:
  - step: 1
    file: "validators/user.py"
    action: "Add email validation function"
    context_needed: ["validators/base.py"]
    validation: "Unit test passes for valid/invalid emails"

  - step: 2
    file: "handlers/registration.py"
    action: "Import and call email validator"
    context_needed: ["validators/user.py lines 1-20"]
    validation: "Integration test passes"

  - step: 3
    file: "tests/test_validators.py"
    action: "Add email validation tests"
    context_needed: ["validators/user.py"]
    validation: "pytest test_validators.py passes"
```

### 2.4 Execution Phase Prompt

For each atomic step:

```
You are an AI assistant with limited context. Your job is to make ONE specific change.

TASK: Add email validation function
FILE: validators/user.py

CONTEXT (provided below):
[paste relevant code from validators/base.py]

INSTRUCTIONS:
1. Add a function called `validate_email` that:
   - Takes a string parameter
   - Returns True if valid email format
   - Returns False otherwise
2. Follow the pattern in base.py
3. Do NOT modify any other functions
4. Do NOT add imports unless necessary

OUTPUT: Only the new/modified code for this file.
```

---

## 3. Single-File Change Discipline

### 3.1 Why Single-File Matters

Limited AI tends to:
- Lose context when reasoning across files
- Make inconsistent changes across files
- Hallucinate imports or dependencies
- Break working code in "related" files

### 3.2 The Discipline

**Rule**: One AI request = One file changed

| Task | Approach |
|------|----------|
| Add new function | AI changes one file |
| Update caller | Separate AI request for caller file |
| Add tests | Separate AI request for test file |
| Update imports | Explicitly list what to import |

### 3.3 Cross-File Coordination

When changes require multiple files, use this workflow:

```
1. Plan all file changes (human or planning AI)
2. Determine correct order (dependencies first)
3. Execute each file change independently
4. Validate after each change
5. If validation fails, review before continuing
```

### 3.4 Example: Adding a New Feature

**Wrong approach** (for limited AI):
> "Add user validation with tests and update the registration handler"

**Right approach**:

```
Request 1: "Add validate_email function to validators/user.py"
[Execute, Validate]

Request 2: "Add test_validate_email to tests/test_validators.py
           Testing validators/user.py:validate_email"
[Execute, Validate]

Request 3: "In handlers/registration.py, import validate_email
           from validators.user and call it on line 42"
[Execute, Validate]
```

---

## 4. Context Window Management

### 4.1 The Problem

Limited context windows mean:
- Can't include entire codebase
- May forget earlier parts of conversation
- Can't reason about large files

### 4.2 Context Prioritization

When context is limited, prioritize:

| Priority | What to Include | Why |
|----------|-----------------|-----|
| 1 (Must) | The file being changed | AI needs to see current state |
| 2 (High) | Direct dependencies | Functions being called |
| 3 (Medium) | Type definitions | Ensure type correctness |
| 4 (Low) | Similar examples | Pattern guidance |
| 5 (Lowest) | General documentation | Background context |

### 4.3 Context Compression Techniques

**Technique 1: Signature-Only Context**

Instead of full file, provide just signatures:

```python
# Full context (uses too many tokens)
def validate_email(email: str) -> bool:
    """Validates email format using regex pattern matching.

    This function checks if the provided string matches...
    [20 more lines of implementation]
    """

# Compressed context
def validate_email(email: str) -> bool:
    """Validates email format. Returns True if valid."""
    # ... implementation
```

**Technique 2: Relevant Snippet Only**

Instead of whole file, provide just the relevant section:

```
# FILE: handlers/registration.py
# SHOWING: Lines 35-55 (around where change needed)

35  async def register_user(request):
36      data = await request.json()
37      email = data.get('email')
38
39      # TODO: Add validation here (LINE 39)
40
41      user = await create_user(email, data.get('name'))
```

**Technique 3: Dependency Summary**

```
# Dependencies available:
# - validators.user.validate_email(str) -> bool
# - validators.user.validate_name(str) -> bool
# - models.User (email: str, name: str, created: datetime)
```

### 4.4 Multi-Turn Context Management

For conversations that span multiple turns:

```
Turn 1: Establish context, get plan
Turn 2: Execute step 1 (include only step 1 context)
Turn 3: Execute step 2 (fresh context for step 2)
...

Each turn is essentially a fresh conversation with focused context.
```

---

## 5. Human-Augmented Reasoning

### 5.1 When AI Reasoning Falls Short

Limited AI may struggle with:
- Complex architectural decisions
- Cross-cutting concerns
- Security implications
- Performance tradeoffs

### 5.2 The Augmentation Pattern

```
┌─────────────────────────────────────────────────────────────┐
│ AI PROPOSES (within capability)                             │
│                                                              │
│ "Here are 3 ways to implement this..."                      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│ HUMAN EVALUATES (complex reasoning)                         │
│                                                              │
│ "Option 2 is best because of security implications          │
│  that require cross-file analysis..."                       │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│ AI EXECUTES (within capability)                             │
│                                                              │
│ "Implementing Option 2 as specified..."                     │
└─────────────────────────────────────────────────────────────┘
```

### 5.3 Prompts for Augmented Workflows

**Proposal Request**:
```
Given this task: [description]
And this context: [code/constraints]

Propose 2-3 implementation approaches.
For each approach:
- Describe the approach in 2-3 sentences
- List pros and cons
- Note any concerns or uncertainties

Do NOT implement yet. I will choose which approach to take.
```

**Execution Request** (after human selects):
```
Implement Approach 2: [description from AI's proposal]

Additional guidance from my review:
- [human insight 1]
- [human insight 2]

Implement in file: [filename]
```

---

## 6. Degraded Capability Fallbacks

### 6.1 Capability Detection

Before starting, assess AI capabilities:

| Capability | Test | Fallback if Limited |
|------------|------|---------------------|
| Multi-file reasoning | Ask to analyze 2 file interaction | Single-file discipline |
| Long context | Include large file | Context compression |
| Planning | Ask for 5-step plan | Human planning |
| Code review | Ask to find bug | Checklist-based review |

### 6.2 Fallback Strategies

**Fallback Matrix**:

| If AI Can't... | Instead Do... |
|----------------|---------------|
| Plan multi-step tasks | Human creates TodoWrite list |
| Reason across files | Process one file at a time |
| Review security | Use security checklist manually |
| Handle large files | Split file or provide snippets |
| Remember context | Start fresh each request |

### 6.3 Graceful Degradation Workflow

```python
# Pseudocode for adaptive workflow

def process_task(task, ai_capabilities):
    if ai_capabilities.can_plan:
        plan = ai.plan(task)
    else:
        plan = human.plan(task)

    for step in plan:
        if ai_capabilities.can_multi_file and step.spans_files:
            result = ai.execute(step)
        else:
            for file in step.files:
                result = ai.execute_single_file(step, file)
                human.validate(result)

    if ai_capabilities.can_review:
        review = ai.review(results)
    else:
        review = human.review_with_checklist(results)
```

---

## 7. Testing with Limited AI

### 7.1 Test Generation Patterns

Limited AI can still help with testing if scoped properly:

**Pattern: Test from Specification**
```
Here is a function specification:

Function: validate_email(email: str) -> bool
- Returns True for valid email format
- Returns False for invalid email format
- Valid format: contains @ and domain

Generate pytest test cases for:
1. Valid email
2. Invalid email (no @)
3. Invalid email (no domain)
4. Edge case: empty string
```

**Pattern: Test from Example**
```
Here is an existing test:
[paste similar test]

Create a similar test for validate_name() function.
Follow the same structure and assertion style.
```

### 7.2 Review AI-Generated Tests

Even with good generation, always verify:

- [ ] Tests actually test the function (not just "no error")
- [ ] Edge cases covered
- [ ] Assertions are meaningful
- [ ] Test names describe behavior

---

## 8. Documentation Patterns

### 8.1 AI-Assisted Documentation

Limited AI can still help document:

**Pattern: Document from Code**
```
Here is a function:
[paste function]

Write a docstring that explains:
- What the function does
- Parameters and their types
- Return value
- Any exceptions raised

Keep it concise (3-5 lines).
```

### 8.2 Human-AI Documentation Workflow

```
1. Human identifies undocumented functions
2. AI generates draft docstrings (one at a time)
3. Human reviews and adjusts
4. AI applies approved docstrings to code
5. Human validates final result
```

---

## 9. Summary: Adapting AOSD for Limited AI

| AOSD Principle | Standard Implementation | Limited AI Adaptation |
|----------------|------------------------|----------------------|
| Multi-Agent Orchestration | Multiple AI agents | Planner/Executor separation |
| Slice-Based Development | AI plans slices | Human plans, AI executes |
| TodoWrite Workflow | AI manages todos | Human manages, AI assists |
| Testing Strategy | AI generates tests | AI generates per-file, human validates |
| Human-in-the-Loop | Strategic oversight | Tactical involvement in every step |
| FCIS Pattern | AI separates concerns | Human guides separation, AI implements |

### Key Takeaways

1. **Decompose aggressively** - Smaller tasks succeed more often
2. **Single-file discipline** - Avoid multi-file AI operations
3. **Explicit context** - Tell AI exactly what it needs to know
4. **Human augmentation** - Fill reasoning gaps manually
5. **Validate continuously** - Check after each AI action

---

## 10. Related Documents

- [AOSD Framework](../../../AOSD_FRAMEWORK.md) - Core methodology
- [Adapting AOSD to Your Environment](../../../AOSD_FRAMEWORK.md#-adapting-aosd-to-your-environment) - Generalized principles
- [AI Prompting Patterns](../../../AI_PROMPTING_PATTERNS.md) - Prompting principles
- [Troubleshooting - AI Agent Issues](../../../TROUBLESHOOTING.md#ai-agent-issues) - Common AI issues

---

**End of Limited AI Capability Patterns**
