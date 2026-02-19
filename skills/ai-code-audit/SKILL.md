---
name: ai-code-audit
description: |
  Audit skill for detecting issues specific to AI-generated code.
  Used after AI code generation, during code reviews, and QA.
  Triggers: "AI code audit", "AI review", "ai audit", "generated code verification"
---

# AI Code Audit

## Goal
Detect common issues specific to AI-generated code and
bring it up to the same quality level as human-written code.

## Instructions

### Step 1: Hallucination Check

Verify that the AI has not used non-existent APIs, methods, or libraries.

- [ ] **Non-existent methods**: Does the called method actually exist in the target class/library?
- [ ] **Wrong signatures**: Do argument count, types, and return types match the actual API?
- [ ] **Non-existent libraries**: Does the imported package actually exist and is it installed?
- [ ] **Deprecated APIs**: Are any deprecated/unsupported APIs being used?
- [ ] **Version mismatch**: Is the API supported in the version used by the project?

**Verification methods:**
- Primary verification via build/compile
- Confirm method existence via IDE autocomplete
- Verify suspicious APIs against official documentation

### Step 2: Consistency Check

Verify that the AI has not introduced patterns different from the existing project style.

- [ ] **Naming conventions**: Does it match the project's existing naming rules?
  - e.g., Project uses `camelCase` but AI used `snake_case`
- [ ] **Architecture patterns**: Does it follow the existing layer structure?
  - e.g., Project uses Repository pattern but service accesses DB directly
- [ ] **Error handling patterns**: Does it handle errors the same way as existing code?
  - e.g., Project uses custom exceptions but AI throws generic Exception
- [ ] **Utility duplication**: Is it re-implementing an already existing utility function?
- [ ] **Configuration approach**: Does it follow existing config management (env, config files, etc.)?

### Step 3: Over-Engineering Check

Verify that the AI has not introduced unnecessary complexity.

- [ ] **Unnecessary abstraction**: Created an interface with only one implementation?
  - Exception: OK if for DI/test mock purposes
- [ ] **Pattern overuse**: Applied Strategy/Factory/Observer pattern to simple logic?
- [ ] **Unnecessary generics**: Is the type parameter actually used with multiple types?
- [ ] **Excessive wrapping**: Are there wrapper classes/functions that only delegate?
- [ ] **YAGNI violation**: Is there "future-proofing" code not required by current requirements?

### Step 4: Completeness Check

Verify that the AI has not left incomplete or missing implementations.

- [ ] **Missing error paths**: Only happy path implemented, failure paths ignored?
- [ ] **Missing edge cases**: Are boundary conditions handled (null, empty, negative, max values)?
- [ ] **Missing resource cleanup**: Are open connections, streams, files being closed?
- [ ] **Residual TODO/FIXME**: Are there incomplete markers left in the code?
- [ ] **Hardcoded values**: Are configurable values hardcoded?
- [ ] **Transaction scope**: Are DB operations within appropriate transaction boundaries?

### Step 5: Report Results

```
🤖 AI Code Audit

🔴 Hallucination (Non-existent API):
  - [file:line] Method/library name → Actual alternative

🟠 Inconsistency (Project mismatch):
  - [file:line] Mismatch type → Align with existing pattern

🟡 Over-Engineering (Unnecessary complexity):
  - [file:line] Type → Simplification suggestion

⚪ Incompleteness (Missing):
  - [file:line] Missing type → Required addition

Verdict: ✅ PASS / ❌ FAIL (FAIL if 🔴 Hallucination ≥ 1)
```

## Constraints
- 🔴 Hallucination is an immediate FAIL — non-existent API calls must always be fixed
- Over-engineering judgment should consider project context — allow if for DI/testing purposes
- Even if the existing project has bad patterns, prioritize "follow existing style"
