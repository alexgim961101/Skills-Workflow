---
name: test-strategy
description: |
  Skill for defining test strategy, writing/running tests, and verifying test quality.
  Used for new feature testing, regression testing after code changes, and test coverage improvement.
  Triggers: "write tests", "test strategy", "regression test", "test plan"
---

# Test Strategy

## Goal
Define an appropriate test strategy for changed code,
write and run tests to verify quality.
**Also verify the quality of the tests themselves** to prevent "tests that pass but are meaningless".

## Instructions

### Step 1: Determine Change Type

Analyze changed files to determine the test mode.

| Situation | Mode | Approach |
|-----------|------|----------|
| New file, no existing tests | **Mode A: New** | Write tests from scratch |
| Existing file modified, tests exist | **Mode B: Update** | Update existing tests + add new cases |
| Refactoring, no behavior change | **Mode C: Verify** | Run existing tests to confirm no regression |

### Step 2: Identify Test Targets

- **Happy Path**: Core success scenarios
- **Edge Cases**: Boundary values, empty input, max values, null/undefined
- **Error Cases**: Exception throwing, timeouts, invalid input
- **Integration Points**: External dependency integration (mock if needed)

### Step 3: Write Tests

**Mode A (New Feature):**
- Follow the project's existing test patterns/framework
- File placement: according to project conventions

**Mode B (Modification):**
1. Load existing tests
2. **Gap Analysis**: Do existing tests cover the new logic?
   - No → Add new test cases
   - API signature change → Update existing test call sites
3. Do not delete existing tests (unless functionality is explicitly deprecated)

**Mode C (Refactoring):**
- No new tests needed; run existing tests to confirm no regression

### Step 4: Run Tests & Analyze

**Execution:**
- Run newly written/modified tests
- Also run related existing test suites (regression testing)

**Result analysis:**

| Result | Type | Action |
|--------|------|--------|
| Compile error | Test code issue | Fix test code |
| New test failure | New feature bug | Report as **BUG** |
| Existing test failure | Regression | Report as **REGRESSION** |
| All pass | Normal | Proceed to Step 5 |

### Step 5: Test Quality Gate

> Even if all tests pass, **⚠️ warn** if the following criteria are not met.

**Assertion quality:**
- [ ] **Empty Test**: Are there tests that only execute without any assertions?
- [ ] **Tautology**: Are there always-true assertions (`assertTrue(true)`)?
- [ ] **Insufficient assertions**: Is the state sufficiently verified after execution? (minimum 1 meaningful assertion)

**Mock quality:**
- [ ] **Excessive mocking**: Is the test target itself being mocked?
- [ ] **Unverified mocks**: Are mocks set up but never verified?
- [ ] **Prefer real behavior**: Even if mockable in unit tests, is there integration testing with real behavior?

**Coverage gaps:**
- [ ] **Missing error paths**: Only happy path tested, failure paths ignored?
- [ ] **Missing boundary values**: Are there tests for 0, null, empty string, max values, etc.?
- [ ] **Missing state transitions**: Are all transition paths tested for stateful objects?

### Step 6: Report Results

```
🧪 Test Report

Mode: [A: New / B: Update / C: Verify]

New tests: N added
Modified tests: M
Run result: ✅ All pass / ❌ N failed

Test Quality:
  Assertion quality: ✅ / ⚠️ (N issue(s))
  Mock quality:      ✅ / ⚠️ (N issue(s))
  Coverage gaps:     ✅ / ⚠️ (N gap(s))

[On failure]
❌ Failure list:
  - [New/Regression] Test name: Failure cause summary

[On quality warnings]
⚠️ Test Quality Warnings:
  - [Empty Test] TestUserCreate: No assertion → Add field validation for created User
  - [Coverage Gap] Error path untested: No exception verification for non-existent userId lookup
```

## Constraints
- Follow the project's existing test framework and patterns
- Do not delete existing tests (unless explicitly deprecated)
- Test names must clearly describe the behavior being tested
- Use mocks minimally; prefer real behavior testing
- Test quality warnings are **improvement suggestions**, not forced fixes — but fix if found repeatedly
