---
description: "Dev — Incrementally implement the approved Plan in chunks, verify builds, and request code review (run in a dedicated session)"
---

# Dev (EXECUTION Session)

Incrementally implement the approved Implementation Plan chunk by chunk.
**Start by reading `.docs/implementation_plan.md`.**

> Start with `task_boundary(Mode=EXECUTION)`

## Pre-Flight

1. **Load Plan**: Read `.docs/implementation_plan.md`.
2. **Build Tool Detection**: Auto-detect `{VERIFY_CMD}` from the project root.

| Language | Detection | Verify Command |
|----------|-----------|----------------|
| Java | `gradlew`, `mvnw` | `./gradlew build` |
| JS/TS | `package.json` | `npm run build` |
| Python | `pytest` | `pytest` |
| Go | `go.mod` | `go build ./...` |
| Rust | `Cargo.toml` | `cargo check` |

## Chunk Loop (Core)

### 1. Split into Chunks

Split the Plan into small, independently buildable and testable units.
Record as a checklist in `.docs/task.md`.

### 2. Implement Chunk → Review Loop

```
For each Chunk:
  Mark [/] in .docs/task.md → Code → Run {VERIFY_CMD}
  ├─ ❌ → Fix and re-run
  └─ ✅ → notify_user to request review
       ├─ "Approved" → Mark [x] → Next Chunk
       ├─ "Changes requested" → Apply feedback and resubmit
       └─ "Stop" → Save progress and exit
```

**Rules:**
- NEVER proceed to the next Chunk without user approval
- Follow existing project conventions ("Fit in")

### 3. Mid-Flight Change Protocol

| Scope | Criteria | Action |
|-------|----------|--------|
| **Minor** | Absorbed within current Chunk | Apply immediately, disclose during review |
| **Medium** | Affects subsequent Chunks | Update Plan → get approval → resume |
| **Major** | Invalidates entire Plan | Record results → start a new `/plan` session |

## Hand-off

```
✅ Implementation Complete

Chunks completed: N/N
Full build: ✅
Next step: Start quality verification with /test in a new session.
```
