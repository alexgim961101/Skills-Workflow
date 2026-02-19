---
name: code-quality-review
description: |
  Skill for deep inspection of changed code quality from SOLID, efficiency, and refactoring perspectives.
  Used for code reviews, PR checks, QA quality gates, and documentation sync verification.
  Triggers: "code review", "quality check", "code review", "SOLID check", "refactoring review"
---

# Code Quality Review

## Goal
Verify that changed code meets quality standards, and
propose specific improvements **including inefficient code and areas needing refactoring**.

## Instructions

### Step 1: Determine Review Scope

- Check the list of changed files (diff-based)
- Review only changed files (not a full codebase review)

### Step 2: Design Quality Check

**SOLID Principles:**
- [ ] **SRP**: Does each class/function have only one responsibility?
- [ ] **OCP**: Is the structure extensible without modifying existing code?
- [ ] **LSP**: Are inheritance/implementations substitutable?
- [ ] **ISP**: Are there unnecessary interface dependencies?
- [ ] **DIP**: Does the upper module not depend directly on lower module implementations?

**Clean Architecture:**
- [ ] Is domain logic independent of framework/UI/DB?
- [ ] Does the dependency direction point inward (toward domain)?
- [ ] Are layer boundaries clear?

### Step 3: Efficiency Check

**Data Access:**
- [ ] **N+1 queries**: Are individual queries being called inside a loop?
- [ ] **Unnecessary full fetch**: Are only needed columns/rows being fetched?
- [ ] **Missing indexes**: Do WHERE/JOIN conditions need indexes?
- [ ] **Cache utilization**: Is the same data being queried repeatedly?

**Algorithm/Logic:**
- [ ] **Unnecessary iteration**: Can nested loops be simplified? (O(n²) → O(n))
- [ ] **Excessive object creation**: Are unnecessary instances created inside loops?
- [ ] **Missing early return**: Can unnecessary computation be avoided with early returns?
- [ ] **Stream/Collection abuse**: Can multiple stream chains be merged into a single pass?

**Resource Management:**
- [ ] **Unreleased connections/streams**: Protected by try-with-resources, defer, using, etc.?
- [ ] **Memory leak patterns**: Are event listeners, callbacks, caches properly cleaned up?

### Step 4: Code Smell & Refactoring Opportunity Detection

When the following patterns are found, suggest specific refactoring approaches.

| Code Smell | Detection Criteria | Refactoring Suggestion |
|------------|-------------------|----------------------|
| **God Object** | Class has 5+ responsibilities, 300+ lines | Split by responsibility |
| **Feature Envy** | Excessively references another object's data | Move logic to the data-owning object |
| **Long Method** | Function 30+ lines or 5+ branches | Decompose with Extract Method |
| **Primitive Obsession** | Domain concepts expressed with primitive types | Introduce Value Objects |
| **Shotgun Surgery** | One change scatters across multiple classes | Consolidate related logic into one module |
| **Duplicated Logic** | Similar code blocks in 2+ places | Extract to common function/utility |
| **Magic Number/String** | Literal values used without constants | Extract Named Constant or Enum |
| **Deep Nesting** | 3+ levels of if/for nesting | Guard Clause, Early Return, extraction |
| **Boolean Parameter** | Function branches behavior on boolean | Split into separate functions or Strategy |

### Step 5: Documentation Sync Check

Verify whether documentation needs updating based on the changes.

- [ ] Are Javadoc/JSDoc/Docstrings updated for changed public APIs?
- [ ] Is Swagger/OpenAPI updated for API signature changes?
- [ ] Are README install/run instructions still valid?

> This only **flags omissions**, not writes the documentation itself.

### Step 6: Severity Classification & Report

| Severity | Criteria | Action |
|----------|----------|--------|
| **🔴 Blocker** | Runtime error, security vulnerability, data loss risk | **Must fix** → FAIL |
| **🟠 Refactor** | Efficiency issue, code smell, refactoring needed | **Must fix** → FAIL |
| **🟡 Warning** | SOLID violation, high coupling, complex branching | Fix recommended |
| **🔵 Info** | Naming improvement, code style, micro-optimization | For reference |

```
📋 Code Quality Review

Files reviewed: N
Findings: 🔴 X / 🟠 Y / 🟡 Z / 🔵 W
Verdict: ✅ PASS / ❌ FAIL (if 🔴 or 🟠 exists)
Doc sync: ✅ OK / ⚠️ N items missing

[Listed by severity]

🔴 [Blocker] Resource leak — ConnectionPool.java
   - Issue: DB connection used without finally block
   - Suggestion: Apply try-with-resources

🟠 [Refactor] N+1 query — UserService.java:45
   - Issue: findById() called individually inside loop (100 users → 100 queries)
   - Suggestion: Change to findAllById() batch query
   - Performance impact: ~100x query reduction

🟠 [Refactor] God Object — OrderService.java
   - Issue: Order creation, payment, notification, inventory all in one class (450 lines)
   - Suggestion: Split into OrderCreationService, PaymentService, NotificationService
   - Pattern: Facade → delegate to individual services

🟡 [Warning] DIP violation — OrderController.java
   - Issue: Direct dependency on OrderRepositoryImpl
   - Suggestion: Inject via OrderRepository interface

⚠️ [Docs] Documentation out of sync — UserController.java
   - Issue: New endpoint POST /users/bulk added but not reflected in API docs
```

**Verdict rules:**
- 🔴 Blocker or 🟠 Refactor **1 or more → ❌ FAIL**
- 🟡 Warning only → ✅ PASS (recorded as recommendations)
- 🔵 Info only → ✅ PASS

## Constraints
- Suggest **specific design patterns/solutions** instead of vague complaints
- Performance issues must state **quantitative impact** (query count, time complexity, etc.)
- Respect the project's existing style and architecture (avoid radical refactoring suggestions)
- 🟠 Refactor only when there is **clear inefficiency/smell** — apply objective criteria, not subjective preference
