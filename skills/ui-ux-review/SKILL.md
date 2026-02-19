---
name: ui-ux-review
description: |
  Skill for reviewing usability, visual consistency, and responsive design from a UI/UX perspective.
  Used for frontend code changes, component additions, and page layout modifications.
  Triggers: "UI review", "UX check", "design review", "ui review"
---

# UI/UX Review

## Goal
Verify that changed UI adheres to usability principles,
is visually consistent, and works correctly across different screens.

## Instructions

### Step 1: Identify Change Scope

- Check the list of changed components/pages
- Distinguish new components vs modifications to existing components
- Check if a design system (tokens, component library) exists

### Step 2: Usability Heuristics (Nielsen's 10)

Inspect only applicable items:

- [ ] **System status visibility**: Is the current state shown to the user (loading, processing, etc.)?
- [ ] **Real-world match**: Do terms, icons, and flow match user expectations?
- [ ] **User control**: Can mistakes be undone? (cancel, back navigation)
- [ ] **Consistency**: Are the same UI patterns used for the same actions?
- [ ] **Error prevention**: Are there UIs that easily lead to mistakes? (confirmation modals for dangerous actions, etc.)
- [ ] **Error recovery**: Do error messages clearly state the problem and solution?
- [ ] **Empty state**: Is there appropriate empty state UI when no data exists?

### Step 3: Visual Consistency

- [ ] **Design token compliance**: Do colors, fonts, spacing use design system tokens?
  - Use `var(--color-error)` instead of hardcoded `#ff0000`
- [ ] **Typography**: Is the font size/weight hierarchy consistent?
- [ ] **Spacing**: Do margins/paddings follow a consistent scale? (4px, 8px, 16px...)
- [ ] **Icons/Images**: Are they using the project's icon set? Consistent sizing?
- [ ] **Component reuse**: Is an existing component available but a new one was created instead?

### Step 4: Interaction Patterns

- [ ] **Loading indicators**: Is there a loading state during API calls? (spinner, skeleton)
- [ ] **Optimistic updates**: Applied where needed?
- [ ] **Form validation**: Are both real-time validation and submit-time validation present?
- [ ] **Feedback**: Is there user feedback after success/failure actions? (toast, notification)
- [ ] **Transitions**: Are there appropriate animations/transitions for state changes?

### Step 5: Responsive Design

- [ ] **Breakpoints**: Is the layout appropriate at major breakpoints?
  - Mobile (< 768px), Tablet (768-1024px), Desktop (> 1024px)
- [ ] **Touch targets**: Are touch areas sufficient on mobile? (minimum 44x44px)
- [ ] **Content overflow**: Are long text and long lists handled properly? (truncation, scrolling)
- [ ] **Images**: Are responsive images used? (srcset or next/image, etc.)

### Step 6: Report Results

```
🎨 UI/UX Review

🔴 Critical (Unusable):
  - [Component] Type: Description → Fix

🟠 Warning (Reduced usability):
  - [Component] Type: Description → Fix

🟡 Suggestion (Improvement recommended):
  - [Component] Type: Description → Improvement

Verdict: ✅ PASS / ❌ FAIL (FAIL if 🔴 Critical ≥ 1)
```

## Constraints
- If a design system exists, always follow it — no arbitrary styling
- Prioritize verifying actual rendering with browser tools
- Do not make subjective aesthetic judgments — inspect only usability and consistency
