---
description: "Test — Define test strategy, write/run tests, and verify test quality (run in a dedicated session)"
---

# Test (VERIFICATION Session 1)

Write and run tests for changed code, then verify the quality of the tests themselves.
**Run after `/dev` session and before `/review` session.**

> Start with `task_boundary(Mode=VERIFICATION)`

## Pre-Flight

1. Read `.docs/implementation_plan.md` to determine the scope of changes.
2. Identify the list of changed files.
3. Confirm `{VERIFY_CMD}` (build tool).
4. **Test Environment Detection**:

| Check | Detection Method |
|-------|-----------------|
| Test framework | `package.json` (jest/vitest), `build.gradle` (JUnit), `requirements.txt` (pytest), etc. |
| Config files | `jest.config.*`, `conftest.py`, `testcontainers.*`, etc. |
| Test directories | `src/test/`, `__tests__/`, `tests/`, etc. |
| Test utilities | Mock libraries, test DB setup, fixtures, etc. |

**If the environment is not set up:**
```
⚠️ Test Environment Not Detected

Missing: [framework / config files / directories]
Suggested setup:
  - [install commands]
  - [config file contents]
  - [directory structure]

→ Proceed with this setup?
```
**Proceed with setup ONLY after user approval. NEVER install automatically.**

## Steps

**Skill: `test-strategy`**

1. **Determine change type**: Decide between New / Update / Verify mode.
2. **Write/update tests**: Happy Path + Edge Cases + Error Cases.
3. **Execute**: Run new tests + existing regression tests.
4. **Test Quality Gate**:
   - Assertion quality (Empty Tests, Tautologies)
   - Mock quality (Excessive mocking, Unverified mocks)
   - Coverage gaps (Error paths, Boundary values, State transitions)

**On FAIL → Fix tests and re-run. On BUG/REGRESSION discovered → return to `/dev` session.**

## Hand-off

```
🧪 Test Complete

New tests: N, Modified: M
Results: ✅ All passing
Test quality: ✅ Assertions OK / Mocks OK / Coverage OK

Next step: Start code review with /review in a new session.
```
