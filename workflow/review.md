---
description: "Review — In-depth code quality, security, AI audit, and performance review (run in a dedicated session)"
---

# Review (VERIFICATION Session 2)

Review changed code from up to 6 perspectives.
**Run after `/test` session.**

> Start with `task_boundary(Mode=VERIFICATION)`

## Pre-Flight

1. Read `.docs/implementation_plan.md` to determine the scope of changes.
2. Identify the list of changed files.
3. **Frontend change detection**: If changed files include `.tsx`, `.jsx`, `.vue`, `.svelte`, `.css`, `.scss`, or `.html`, activate Gates 5 and 6.

## Review Pipeline (4 + 2 Conditional Gates)

**If any Gate FAILs → return to `/dev` session for fixes.**

### Gate 1: Code Quality

**Skill: `code-quality-review`**

SOLID principles + efficiency + 9 code smells + documentation sync.

### Gate 2: Security

**Skill: `security-review`**

Check only applicable areas: Input validation / Auth & authorization / Data protection / Infrastructure & config.

### Gate 3: AI Code Audit

**Skill: `ai-code-audit`**

Hallucinated API verification → Project consistency → Over-engineering detection → Completeness check.

### Gate 4: Performance

**Skill: `performance-review`**

Check only applicable areas: Algorithms / DB / I/O / Memory / Concurrency.

### Gate 5: UI/UX (Frontend changes only)

**Skill: `ui-ux-review`**

Usability heuristics + visual consistency + interaction patterns + responsive design.

### Gate 6: Accessibility (Frontend changes only)

**Skill: `accessibility-review`**

WCAG 2.1 AA: Semantic HTML + keyboard navigation + ARIA + color contrast.

## Final

Run full `{VERIFY_CMD}` (integrated build/test).
If deployment is involved, apply the `health-check` skill.
Write results to `.docs/walkthrough.md`.

## Report

```
📋 Review Complete

Gate 1 (Quality):       ✅ PASS — 🔴 0, 🟠 0
Gate 2 (Security):      ✅ PASS — 🔴 0
Gate 3 (AI Audit):      ✅ PASS — Hallucinations: 0
Gate 4 (Performance):   ✅ PASS — 🔴 0
Gate 5 (UI/UX):         ✅ PASS — or ⏭️ SKIP (backend-only changes)
Gate 6 (Accessibility): ✅ PASS — or ⏭️ SKIP (backend-only changes)
Full build:             ✅ PASS

Deliverable: .docs/walkthrough.md
```
