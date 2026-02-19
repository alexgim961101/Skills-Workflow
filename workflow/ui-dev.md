---
description: "UI Dev — Component-based incremental implementation, browser verification, and design system compliance (run in a dedicated session)"
---

# UI Dev (EXECUTION Session — Frontend)

Incrementally implement UI using a component-based approach.
**Start by reading `.docs/implementation_plan.md`.**

> Start with `task_boundary(Mode=EXECUTION)`

## Pre-Flight

1. **Load Plan**: Read `.docs/implementation_plan.md`.
2. **Dev Server Detection**: Detect the development server in the project.

| Framework | Detection | Dev Server |
|-----------|-----------|------------|
| Next.js | `next.config.*` | `npm run dev` |
| Vite | `vite.config.*` | `npm run dev` |
| CRA | `react-scripts` in package.json | `npm start` |
| Vue CLI | `vue.config.*` | `npm run serve` |
| Nuxt | `nuxt.config.*` | `npm run dev` |

3. **Design System Check**: Verify whether the project has design tokens or a component library.
   - CSS Variables, Theme files, Storybook, etc.

## Component Chunk Loop (Core)

### 1. Component-Based Chunk Splitting

**Splitting principles (different from backend):**
- Atomic → Composite order: **base components → composite components → pages**
- Each Chunk must be visually verifiable in the browser
- Styles and logic are included in the same Chunk
- Responsiveness is handled during component implementation (not deferred)

Record as a checklist in `.docs/task.md`.

### 2. Implement Chunk → Visual Verification → Review Loop

```
For each Chunk:
  Mark [/] in .docs/task.md → Code
  ├─ 1. Implement component (markup + styles + logic)
  ├─ 2. Verify design system token usage
  ├─ 3. Visual check in browser
  │     ├─ Desktop view
  │     ├─ Mobile view (responsive)
  │     └─ Interaction behavior (hover, click, forms)
  ├─ 4. Build verification ({VERIFY_CMD})
  │     ├─ ❌ → Fix and re-run
  │     └─ ✅ ↓
  └─ 5. notify_user to request review (include screenshots)
       ├─ "Approved" → Mark [x] → Next Chunk
       ├─ "Changes requested" → Apply feedback and resubmit
       └─ "Stop" → Save progress and exit
```

**Key differences from backend `/dev`:**
- **Browser visual check** step added before build verification
- **Screenshots** included in review requests (via generate_image or browser tools)
- **Responsive check** included in every Chunk

### 3. Mid-Flight Change Protocol

Same Minor / Medium / Major protocol as `/dev`.
For design changes specifically:

| Scope | Example | Action |
|-------|---------|--------|
| **Minor** | Color change, padding adjustment | Apply in current Chunk |
| **Medium** | Layout change, new component added | Update Plan → re-split Chunks |
| **Major** | Full design overhaul | Start a new `/plan` session |

## Hand-off

```
✅ UI Implementation Complete

Chunks completed: N/N (N components, M pages)
Responsive: Desktop ✅ / Mobile ✅
Design system compliance: ✅

Next step: Start quality verification with /test → /review in a new session.
```
