---
name: performance-review
description: |
  Skill for deep analysis of code performance bottlenecks and optimization recommendations.
  Used for performance-sensitive code changes, large data processing, and query optimization.
  Triggers: "performance check", "performance review", "optimization", "slow", "perf review"
---

# Performance Review

## Goal
Detect performance bottlenecks in changed code and
provide optimization recommendations with measurable evidence.

## Instructions

### Step 1: Determine Performance Impact

Check whether the changed code falls into the areas below.
**Only inspect applicable areas.**

| Area | Applicable when |
|------|-----------------|
| **Algorithm/Data structure** | Loops, sorting, searching, large data processing |
| **Database** | Query add/change, ORM usage, data access patterns |
| **I/O/Network** | File processing, API calls, external service integration |
| **Memory** | Large objects, caching, collection manipulation |
| **Concurrency** | Threads, async, locks, connection pools |

### Step 2: Algorithm/Data Structure

- [ ] **Time complexity**: Is the Big-O of core logic appropriate?
  - Nested loops → check if improvement possible when O(n²) or above
  - Is the appropriate algorithm used for sorting/searching?
- [ ] **Data structure selection**: Is the data structure appropriate for the usage pattern?
  - Frequent lookups → HashMap/Set usage?
  - Frequent inserts/deletes → LinkedList/Queue suitability?
  - Duplicate checks → Set instead of List.contains()?
- [ ] **Unnecessary iteration**: Is the same data being iterated multiple times?
  - Can Stream chaining enable single-pass?
  - Can intermediate results be cached?

### Step 3: Database

- [ ] **N+1 problem**: Are queries being executed inside a loop?
  - Does ORM lazy loading cause N+1?
  - → Change to Fetch Join, batch query, IN clause
- [ ] **Indexes**: Do columns in WHERE/JOIN/ORDER BY have indexes?
  - Is the column order in composite indexes appropriate?
- [ ] **Query efficiency**: Are only needed columns being SELECTed? (avoid SELECT *)
  - Pagination: OFFSET vs Cursor approach
  - Large aggregations: app-level vs DB-level
- [ ] **Transaction scope**: Is the transaction unnecessarily long?
  - Are external API calls included inside the transaction?

### Step 4: I/O / Network

- [ ] **Synchronous blocking**: Are external API calls executing synchronously?
  - Can independent external calls be parallelized?
- [ ] **Batch processing**: Can batch APIs be used instead of per-item API calls?
- [ ] **Retry/Timeout**: Do external calls have timeout and retry policies?
  - Prevent indefinite waiting
- [ ] **File I/O**: Are large files streamed instead of loaded entirely into memory?
- [ ] **Caching**: Is caching applied to avoid repeating identical requests?
  - Is the cache invalidation strategy appropriate?

### Step 5: Memory

- [ ] **Large collections**: Are results streamed/paged instead of loaded entirely into memory?
- [ ] **Object lifecycle**: Are unnecessarily large objects created inside loops?
- [ ] **Memory leaks**: Are event listeners, callbacks, caches being cleaned up?
  - No patterns of unbounded accumulation in static collections?
- [ ] **String concatenation**: Are strings not being repeatedly concatenated with `+` inside loops?
  - → Use StringBuilder / StringBuffer / join

### Step 6: Concurrency

- [ ] **Lock contention**: Are locks held over a wider scope than necessary?
  - Can read/write locks be separated?
  - Can lock-free data structures (ConcurrentHashMap, etc.) be used?
- [ ] **Connection pool**: Are DB/HTTP connection pool sizes appropriate?
  - Wait time/timeout settings on pool exhaustion
- [ ] **Thread safety**: Is synchronization appropriate for shared state?
- [ ] **Async leak**: Are exceptions from async operations being silently ignored?

### Step 7: Report Results

```
⚡ Performance Review

Reviewed areas: [Algorithm / DB / I/O / Memory / Concurrency] applicable areas

🔴 Critical (Potential performance failure):
  - [file:line] Type: Description
    Current: O(n²) nested loop
    Suggestion: Can convert to O(n) with HashMap

🟠 Warning (Bottleneck expected under load):
  - [file:line] Type: Description → Improvement

🟡 Suggestion (Optimization recommended):
  - [file:line] Type: Description → Improvement

Verdict: ✅ PASS / ❌ FAIL (FAIL if 🔴 Critical ≥ 1)
```

## Constraints
- Optimization without measurement is guesswork — provide benchmark/profiling evidence when possible
- Beware premature optimization — micro-optimizations on non-bottlenecks are 🟡 Suggestion only
- Readability tradeoff — state explicitly when performance improvement severely hurts readability
- Only inspect applicable areas — skip areas unrelated to the change
