---
name: debugging
description: |
  Systematic debugging skill for bug reproduction, root cause analysis, and fix scope determination.
  Used for bug report response, error investigation, and incident analysis.
  Triggers: "bug fix", "error analysis", "debugging", "incident root cause", "debug"
---

# Debugging

## Goal
Systematically trace the root cause of a bug and
determine the fix scope based on reproducible evidence.

## Instructions

### Step 1: Symptom Summary

Extract the following from the bug report or user description:

- **What**: What behavior/result is abnormal?
- **When**: Always? Only under specific conditions? Since a recent deployment?
- **Where**: Which endpoint/screen/service?
- **Error info**: Error message, stack trace, HTTP status code, logs

```
🐛 Bug Summary
Symptom: [What is going wrong and how]
Reproduction conditions: [Known conditions / Unknown]
Error: [Message or code]
Recent changes: [Related deployment/commit if any]
```

### Step 2: Reproduce

Always reproduce the bug before fixing it.

**Reproduction strategies:**

| Situation | Approach |
|-----------|----------|
| Clear error message | Trace directly to error location in code |
| Occurs only with specific input | Write test case with that input |
| Intermittent occurrence | Analyze logs, suspect concurrency/timing |
| Environment-dependent | Compare env variables, config, dependency versions |

**Reproduction confirmation:**
- [ ] Can the bug be reproduced locally?
- [ ] Has a reproducible test case been written? (a failing test)

> If it cannot be reproduced, do not fix it. Request additional information from the user.

### Step 3: Root Cause Analysis (RCA)

Find the **cause**, not the symptom.

**Analysis techniques:**

| Technique | When to use | Method |
|-----------|-------------|--------|
| **Backtracking** | Error location is clear | Trace call chain top→down from stack trace |
| **Binary search** | Unclear when it broke | `git bisect` or compare logs by time period |
| **Hypothesis testing** | Multiple cause candidates | Hypothesis → verify with logs/code → eliminate → repeat |
| **Isolation** | Complex system | Remove dependencies one by one to identify the causal module |
| **Data tracing** | Incorrect result values | Identify the corruption point along input → transformation → output path |

**Root cause classification:**

| Type | Example | Fix Complexity |
|------|---------|---------------|
| **Logic error** | Condition mistake, off-by-one, null not handled | Low |
| **State issue** | Race condition, cache inconsistency, session corruption | High |
| **Data issue** | Bad migration, corrupted data | Medium–High |
| **Environment/Config** | Env variable, version mismatch, dependency conflict | Low |
| **External dependency** | API change, library bug, infrastructure outage | Variable |

**Output:**
```
🔍 Root Cause Analysis

Cause type: [Logic error / State issue / ...]
Location: [filename:line] or [module/service name]
Description: [Why this code is the problem, specifically]
Evidence: [Reproduction test, logs, code path]
```

### Step 4: Determine Fix Scope

After identifying the root cause, assess the scope of the fix.

- [ ] Does this fix affect other features?
- [ ] Does the same bug pattern exist elsewhere? (Shotgun Fix needed?)
- [ ] Does the fix change an API contract or data structure?

**Classification by scope:**

| Scope | Criteria | Approach |
|-------|----------|----------|
| **Isolated** | Single file/function, no impact elsewhere | Fix immediately |
| **Spreading** | Same pattern exists in multiple places | List all occurrences, batch fix |
| **Structural** | Design flaw is the cause, refactoring needed | Create plan first (consider switching to full-dev-cycle) |

## Constraints
- Do not fix without reproduction — do not change code based on "this is probably the cause"
- Target root cause fix (Root Fix), not symptom fix (Symptom Fix)
- If fix scope is Structural, notify the user and recommend switching to full-dev-cycle
