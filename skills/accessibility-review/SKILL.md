---
name: accessibility-review
description: |
  Skill for checking web accessibility (a11y) against WCAG 2.1 standards.
  Used for frontend code changes, form/modal/navigation implementations.
  Triggers: "accessibility review", "a11y", "accessibility", "WCAG", "screen reader"
---

# Accessibility Review

## Goal
Verify that changed UI meets WCAG 2.1 AA criteria and
ensure that users with disabilities can use it equally.

## Instructions

### Step 1: Identify Change Scope

- Identify elements that affect accessibility in the changed components
- Focus on interactive elements (buttons, links, forms, modals, dropdowns)

### Step 2: Semantic HTML

- [ ] **Correct tags**: Use `<button>` instead of `<div onclick>`
- [ ] **Heading hierarchy**: `<h1>` → `<h2>` → `<h3>` in order (no skipping levels)
- [ ] **Landmarks**: Proper use of `<nav>`, `<main>`, `<aside>`, `<footer>`
- [ ] **Lists**: Use `<ul>/<ol>/<li>` for list data
- [ ] **Tables**: Use `<th>`, `scope`, `caption` for data tables

### Step 3: Keyboard Navigation

- [ ] **Focusable**: Are all interactive elements accessible via keyboard?
- [ ] **Focus order**: Does Tab order match the visual order?
- [ ] **Focus indicator**: Is the focused element visually distinguishable? (do not remove `outline`)
- [ ] **No keyboard trap**: Can you Tab out of modals/dropdowns?
- [ ] **Shortcuts**: Do Enter (confirm), Escape (close), arrow keys (list navigation) work?

### Step 4: ARIA & Screen Reader

- [ ] **alt text**: All `<img>` have meaningful `alt` attributes (decorative images use `alt=""`)
- [ ] **aria-label**: Text-less buttons (icon buttons) have `aria-label`
- [ ] **aria-live**: Dynamically changing content has `aria-live` set (toasts, notifications)
- [ ] **role**: Custom components have appropriate `role` assigned (dialog, tablist, menu, etc.)
- [ ] **aria-expanded/selected**: Do toggles, accordions, tabs convey their state?
- [ ] **Form labels**: All `<input>` have associated `<label>` or `aria-labelledby`

### Step 5: Visual Accessibility

- [ ] **Color contrast**: Text/background contrast ratio 4.5:1 or above (AA standard)
  - Large text (18px+): 3:1 or above
- [ ] **No color-only information**: Is information not conveyed by color alone? (use icons/text alongside)
- [ ] **Text resizing**: Does the layout remain intact when zoomed up to 200%?
- [ ] **Reduced motion**: Respond to `prefers-reduced-motion` media query

### Step 6: Report Results

```
♿ Accessibility Review (WCAG 2.1 AA)

🔴 Critical (Inaccessible):
  - [Component] Violation: Description → Fix

🟠 Warning (Reduced accessibility):
  - [Component] Criterion: Description → Fix

🟡 Best Practice:
  - [Component] Recommendation: Description → Improvement

Verdict: ✅ PASS / ❌ FAIL (FAIL if 🔴 Critical ≥ 1)
```

## Constraints
- Based on WCAG 2.1 AA (AAA is recommendation only)
- Semantic HTML first — ARIA only when native HTML cannot solve the issue
- Recommend combining automated tools (axe, lighthouse) with manual inspection
