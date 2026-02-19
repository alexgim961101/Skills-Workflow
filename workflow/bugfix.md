---
description: "Bugfix — Bug reproduction, root cause analysis, fix, and regression testing in a lightweight cycle"
---

# Bugfix Cycle

**Objective:** Systematically diagnose bugs, apply safe fixes, and prevent regressions.
A lightweight cycle compared to the full `/plan` → `/dev` → `/review` pipeline — starts directly from diagnosis without upfront planning.

## Flow Overview

```
┌───────────────────────────────────────────────────┐
│  DIAGNOSE                                          │
│  debugging skill: Symptom → Reproduce → Root Cause │
│  If scope is Structural → switch to /plan session  │
├───────────────────────────────────────────────────┤
│  FIX                                               │
│  Impact analysis → Code fix → Build verification   │
│  → User review                                     │
├───────────────────────────────────────────────────┤
│  VERIFY                                            │
│  Regression tests + Fix validation + Code quality  │
└───────────────────────────────────────────────────┘
```

---

## Phase 1: DIAGNOSE

Start with `task_boundary(Mode=PLANNING)`.

**Skill: `debugging`**

1. **Document symptoms:** Gather error messages, trigger conditions, and affected scope.
2. **Reproduce:** Reproduce the bug locally and write a failing test case.
3. **Root cause analysis:** Use backtracking, bisection, and hypothesis testing to pinpoint the cause.
4. **Determine fix scope:**
   - **Isolated / Spreading** → Proceed to Phase 2
   - **Structural** → Notify user and recommend switching to a `/plan` session

Present the diagnosis to the user:
```
🐛 Bug Diagnosis

Cause: [root cause summary]
Location: [file:line]
Scope: [Isolated / Spreading / Structural]
Fix approach: [how it will be fixed]

→ Proceed with this fix?
```

---

## Phase 2: FIX

Switch to `task_boundary(Mode=EXECUTION)`.

### Step 1: Impact Analysis

**Skill: `impact-analysis`** (when scope is Spreading or higher)

- Check if the same bug pattern exists elsewhere
- Assess how the fix affects other features

### Step 2: Code Fix

1. Fix the root cause (not just the symptoms).
2. If Spreading, fix all occurrences.
3. Run `{VERIFY_CMD}` to confirm the build passes and existing tests still pass.

### Step 3: User Review

Use `notify_user` to request review of the fix.
- Approved → Proceed to Phase 3
- Changes requested → Apply and resubmit

---

## Phase 3: VERIFY

Switch to `task_boundary(Mode=VERIFICATION)`.

### Step 1: Fix Validation

- Confirm the **failing test from Phase 1 now passes**
- This is the core proof that the bug is fixed

### Step 2: Regression Testing

**Skill: `test-strategy`** (Mode B or C)

- Run the existing test suite on the fixed code
- Add new regression tests to prevent the same bug from recurring

### Step 3: Code Quality Check

**Skill: `code-quality-review`** (scoped to changed files only)

- Verify the fix does not introduce new code smells
- Confirm it is a **clean fix**, not a hotfix hack

```
✅ Bugfix Complete

Diagnosis: [cause summary]
Fix: [N files changed]
Validation: Failing test → now passing ✅
Regression: All existing tests passing ✅
Quality: 🔴 0, 🟠 0 ✅
```
