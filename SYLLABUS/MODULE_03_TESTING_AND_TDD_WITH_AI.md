# Module 03  Testing & TDD with AI
**AOSD Curriculum**
**Fast/Medium/Slow Tests, TDD with AI, and End-to-End Resilience**

---

## 1. Purpose of This Module

This module teaches the full AOSD approach to **testing in an AOSD development environment**.

You will learn:

- How to use the Fast/Medium/Slow test model
- How to practice TDD with Claude Code
- How to structure tests so AI can reason about them
- How to extract pure functions for fast tests
- How to validate AWS interactions with medium tests
- How to build robust integration tests that catch bundling & DDB issues
- How to use Playwright in the outer loop
- How to use ChatGPT to review test plans
- How to ensure constrained environment edits don't break test structure

Testing is the backbone of AOSD  without strong tests, AI-generated code becomes unpredictable.

---

# 2. The AOSD Test Philosophy

AOSD uses testing for **stability and predictability**, not just correctness.

The philosophy:

1. **AI moves fast � tests must anchor behavior**
2. **Fast tests must dominate**
3. **Integration tests prevent catastrophic regressions**
4. **Testing is part of architecture**
5. **Tests shape the code AI produces**
6. **Playwright sits on top as the behavioral "truth" layer**
7. **Tests enforce invariants indirectly**

Testing is not optional  it is *how AOSD systems stay sane*.

---

# 3. The Fast / Medium / Slow Test Model (AOSD Standard)

This module builds directly on the MEMORY pattern card: `TEST_STRATEGY_FAST_MEDIUM_SLOW.md`.

### **3.1 Fast Tests (Pure Functions)**
- No AWS
- No network
- No mocks
- Deterministic inputs/outputs
- Complete in ~23 minutes for whole suite
- Run constantly during development

### **3.2 Medium Tests (Mocked AWS)**
- Moto/stubs via AWS Factory
- Handler logic
- Logic that touches AWS indirectly
- Runs in ~5 minutes

### **3.3 Slow Tests (Integration)**
- Bundle structure validation
- PK/SK enforcement
- Lambda import correctness
- DDB table behavior
- Runs in ~2 minutes

### **3.4 Playwright (Outer Loop  Jim Workflow)**
- Runs on laptop
- Full E2E
- Dev � full suite
- Prod � smoke tests only
- (Optional for others; required for Jim)

---

# 4. TDD with AI (AOSD Approach)

AOSD TDD is not the same as traditional TDD.

### **4.1 Sequence of Work**
1. Write (or ask ChatGPT to write) expected behavior
2. Ask Claude Code:
   > "Write fast tests for this behavior first."
3. Claude writes pure-function tests
4. Claude implements code
5. Claude writes medium tests
6. Claude implements AWS-facing logic
7. Claude writes slow integration tests
8. Validate bundle structure
9. Deploy to development environment
10. Playwright from laptop

### **4.2 Why this works**
AI learns from the tests.
Tests shape the implementation, architecture, and future slices.

### **4.3 What *never* to do**
- Never write code without tests
- Never let Claude skip fast tests
- Never approve code where tests were not updated

---

# 5. Extracting Pure Functions (AI-Friendly TDD)

AI performs better when logic is separated:

- **Functional core** (pure functions � fast tests)
- **Imperative shell** (AWS calls, I/O � medium/slow tests)

You will learn to:

- Ask Claude to extract pure logic into a helper
- Write fast tests against the pure logic
- Test the rest with mocks
- Minimize AWS interactions
- Keep business logic away from handlers

This pattern is essential for maintainability.

---

# 6. Writing Test Plans with ChatGPT

ChatGPT is excellent at designing test plans.

Ask:

> "Design fast/medium/slow tests for this slice."

ChatGPT will:

- Identify missing edge cases
- Suggest invalid-input tests
- Suggest negative tests
- Validate PK/SK behavior
- Suggest items for Playwright

Claude Code will then implement the actual test files.

ChatGPT = test designer
Claude Code = test implementer

---

# 7. Using Claude Code to Write and Update Tests

Claude Code must:

- Write fast tests first
- Use AWS Factory for all mocked AWS calls
- Maintain directory structure:
```
tests/unit/
tests/integration/
```
- Ensure pure functions are isolated
- Maintain determinism
- Avoid test brittleness
- Never remove tests without permission
- Ensure slow tests validate bundling structure

Claude Code naturally writes tests well **if the slice spec is clear**.

---

# 8. Playwright as the Top of the Pyramid (Jim Workflow)

Playwright tests:

- run **only** from the laptop
- hit Dev/Staging/Prod URLs
- validate end-to-end flows
- act as the highest-level correctness layer

### Dev:
- destructive allowed
- full suite

### Staging (future):
- full regression suite

### Prod:
- smoke tests only
- must avoid destructive flows

This is a personal practice today, but an excellent discipline for ensuring correctness.

---

# 9. Architectural Influence of Tests

Your test strategy shapes your architecture:

- Pure logic � smaller, testable modules
- AWS interactions � thin wrappers
- PK/SK logic � pure functions + handler tests
- Decision logic � unit tests with catalog-driven data
- Prompt builders � deterministic formatting tests
- Bundling � integration tests

Tests are a **design constraint** that ensures safe AI evolution.

---

# 10. Testing in Constrained Environments

Constrained environments cannot run complex test suites.

Constrained AI tools must:

### Allowed:
- update one small test file
- fix expectations
- fix a typo
- adjust 1-line mocks

### Forbidden:
- creating new test classes
- modifying test suite structure
- adding multi-file test dependencies
- modifying fixtures or moto infrastructure
- writing integration tests
- modifying bundling tests

Large test updates must be done in the primary development environment.

---

# 11. Exercises

### Exercise 1  Pure Function Extraction
Select a handler in your project.
Extract one piece of logic into a pure function.
Write fast tests for it.

### Exercise 2  Medium Test Design
Take an API handler and ask ChatGPT to design medium tests.

### Exercise 3  Slow Test Understanding
Pick a slow test and identify:
- what bundling misconfiguration it would catch
- what DDB issues it would catch

### Exercise 4  Playwright Flow
Identify one Dev flow and write a simple Playwright test for it.

---

# 12. Completion Criteria

You've mastered this module when you can:

- Reliably design and enforce fast/medium/slow test patterns
- Use ChatGPT to create good test coverage plans
- Use Claude Code to implement tests correctly
- Keep business logic in pure functions
- Integrate TDD practices naturally into slice-based work
- Use Playwright effectively in Dev and Prod
- Ensure constrained environment edits don't break test strategy
- Recognize that tests anchor AOSD development

Next up:
**Module 04  Environments & Operations**

---

**End of MODULE_03_TESTING_AND_TDD_WITH_AI.md**
