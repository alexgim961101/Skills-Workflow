---
description: "Plan — Analyze requirements, validate design, and produce an Implementation Plan (run in a dedicated session)"
---

# Plan (PLANNING Session)

Analyze requirements, validate the design, and produce an actionable Implementation Plan.
Once complete, **start implementation with `/dev` in a new session**.

> Start with `task_boundary(Mode=PLANNING)`

## Steps

### 1. Resolve Ambiguity

**Skill: `ambiguity-checklist`**

Classify risk (Critical / Standard / Low) → inspect each item → present A/B/C options for any ambiguous points.
Repeat until every required item reaches "Implementable" status.

### 2. Impact Analysis

**Skill: `impact-analysis`**

Identify change targets → trace dependencies (Blast Radius) → determine breaking changes.

### 3. Design Validation

**Skill: `logic-audit`**

Run only the applicable checks: State Machine / Concurrency / Data Integrity / Race Condition.

### 4. Rollback Strategy

**Skill: `rollback-strategy`**

Design a rollback plan when the change involves DB migrations, deployments, or infrastructure changes.

### 5. Generate Implementation Plan

> [!CAUTION]
> **All artifacts MUST be created under `{PROJECT_ROOT}/.docs/`.**
> Creating `implementation_plan.md`, `task.md`, or `walkthrough.md` in `appDataDir/brain/`, `~/.gemini/`, or any other external path is **strictly prohibited**.
> This rule OVERRIDES Antigravity's default artifact path settings.

**Procedure:**

1. Create the `{PROJECT_ROOT}/.docs/` directory if it does not exist.
2. Write the plan to `{PROJECT_ROOT}/.docs/implementation_plan.md`.
3. Write the task checklist to `{PROJECT_ROOT}/.docs/task.md`.
4. **Verify:** Use `list_dir` or `find_by_name` to confirm `.docs/implementation_plan.md` actually exists under the project root.
5. Use `notify_user` to request approval, explicitly stating the path **`{PROJECT_ROOT}/.docs/implementation_plan.md`**.

**Follow this structure:**

```markdown
# [Goal Description]

## Summary
## User Review Required (if applicable)
## Proposed Changes
### [Component Name]
#### [MODIFY/NEW/DELETE] [file basename](file:///absolute/path)
## Verification Plan
### Automated Tests
### Manual Verification
```

## Hand-off

```
✅ Plan Complete

Deliverables:
  - .docs/implementation_plan.md
  - .docs/task.md
Next step: Start implementation with /dev in a new session.
```
