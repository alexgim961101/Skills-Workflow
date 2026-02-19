---
name: ambiguity-checklist
description: |
  Systematic checklist for identifying ambiguities in user requirements.
  Used for new feature requests, spec definitions, requirements analysis, and API design.
  Triggers: "feature request", "spec writing", "requirements analysis", "start design"
---

# Ambiguity Checklist

## Goal
Determine whether requirements are at an "Implementable" level.
Resolve ambiguities by presenting specific options to the user.

## Instructions

### Step 1: Risk Classification
First, determine the risk level of the request. Inspection depth varies by risk level.

| Risk Level | Criteria | Required Checks |
|------------|----------|-----------------|
| **Critical** | Payment, authentication, personal data, financial data | All 6 mandatory |
| **Standard** | General business features, API CRUD | 1–4 mandatory, 5–6 recommended |
| **Low** | UI text changes, config adjustments, doc edits | Only 4 mandatory, rest can be skipped |

### Step 2: Detailed Inspection per Item

Inspect each item following the guide below.
If an item is ambiguous, present it to the user as a **"multiple-choice question"**.
(Instead of open-ended "How should we handle this?", present specific options A/B/C)

---

#### 1. Error Scenarios (Failure Modes)
**Check**: Have all possible external failures for this feature been identified?

**Detailed checklist**:
- [ ] Behavior on network failure (timeout, retry, fallback)
- [ ] Behavior when external API/service is down
- [ ] Behavior on DB connection failure / query timeout
- [ ] Error response format on input validation failure
- [ ] Behavior on partial failure (what if only 3 out of 10 succeed?)

**Pass criteria**: For each failure, "which HTTP code + which message + which follow-up action" is defined

**Example question if not met**:
```
What should happen when the external payment API times out?
  A) Retry 3 times, then return failure response ("Please try again later")
  B) Fail immediately + notify admin
  C) Queue it for background retry
```

---

#### 2. Input Constraints
**Check**: Are boundary values defined for all input fields?

**Detailed checklist**:
- [ ] Type of each field (string, number, enum, file, etc.)
- [ ] Required vs optional for each field
- [ ] Strings: min/max length, allowed characters (Unicode? special chars? emoji?)
- [ ] Numbers: min/max value, decimal allowed?
- [ ] Files: max size, allowed formats, concurrent upload count
- [ ] Arrays/Lists: min/max item count, duplicates allowed?

**Pass criteria**: Constraints specific enough to be converted to a DTO/Schema for each input field

**Example question if not met**:
```
Please define constraints for the username field:
  - Min length: 2 chars? 3 chars?
  - Max length: 20 chars? 30 chars?
  - Allowed characters: A) Alphanumeric only  B) Include Unicode  C) Include special chars  D) Include emoji
  - Uniqueness: A) Must be unique  B) Duplicates allowed
```

---

#### 3. Auth/Permission (Access Control)
**Check**: Who can use this feature, and who cannot?

**Detailed checklist**:
- [ ] Authentication required? (Can unauthenticated users access it?)
- [ ] Access scope per role (Admin / Manager / User / Guest)
- [ ] Resource ownership (Own data only? Same team? Everyone?)
- [ ] Special permissions (Super admin override, delegation, etc.)

**Pass criteria**: Expressible as a Role × Action matrix

**Example question if not met**:
```
Please define access permissions for this API:

| Role  | Create | Read | Update | Delete |
|-------|--------|------|--------|--------|
| Admin | ?      | ?    | ?      | ?      |
| User  | ?      | ?    | ?      | ?      |
| Guest | ?      | ?    | ?      | ?      |

Specifically:
- Can a User view another User's data?
  A) Own data only  B) Within same team  C) Publicly visible
```

---

#### 4. Business Logic (Success Criteria)
**Check**: How do we determine "this feature succeeded"?

**Detailed checklist**:
- [ ] Core success scenario (Happy Path) is clear
- [ ] Business rules (conditional logic) are specified
- [ ] State transition rules are defined, if applicable
- [ ] Side Effects: notifications, logs, event publishing, etc.
- [ ] Idempotency: what happens if the same request is sent twice?

**Pass criteria**: Scenarios can be written in Given-When-Then format

**Example question if not met**:
```
Confirming business rules for order cancellation:
- Cancel deadline: A) Before shipping  B) Within 24 hours of payment  C) Always allowed
- Refund method: A) Immediate full refund  B) Partial refund (fees deducted)  C) Convert to points
- Inventory after cancel: A) Immediate restore  B) Restore after inspection
- Notification: A) Email  B) Push  C) SMS  D) None
```

---

#### 5. Performance (Performance Requirements) — Required for Standard+
**Check**: Are expected load and response time requirements defined?

**Detailed checklist**:
- [ ] Expected concurrent users / requests per second (TPS)
- [ ] Response time targets (p50, p95, p99)
- [ ] Expected data size (table row count after 1 year)
- [ ] Pagination / infinite scroll needed?

**Pass criteria**: Specific numbers exist, or an explicit decision like "small scale, no performance optimization needed"

**Example question if not met**:
```
Please define performance requirements:
- Expected user scale: A) Small (<100)  B) Medium (<10,000)  C) Large (10,000+)
- Caching, pagination, and indexing strategies depend on this.
- If performance optimization is unnecessary at this stage, say "skip".
```

---

#### 6. Idempotency — Required for Critical
**Check**: Is it safe if the same request is executed multiple times?

**Detailed checklist**:
- [ ] Handling duplicate requests from network retries
- [ ] Need for Idempotency Key
- [ ] Duplicate creation prevention (unique constraint / dedup logic)
- [ ] Preventing duplicate deductions for payment/points

**Pass criteria**: "If this API is called twice with the same parameters, X happens" is clear

**Example question if not met**:
```
If this API is called twice due to network issues:
  A) Returns the same result (idempotent) — use Idempotency Key
  B) Two items are created (non-idempotent) — acceptable
  C) Second call returns 409 Conflict error
```

---

### Step 3: Final Verdict

After inspecting all required items, make a verdict:

| Status | Condition | Action |
|--------|-----------|--------|
| ✅ **Implementable** | All required items for the risk level are met | Proceed with design |
| ⚠️ **Conditionally OK** | 1–2 items unmet but reasonable defaults exist | Propose defaults, get user confirmation |
| ❌ **Not Ready** | Core items unmet | Present questions, do not start design |

### Step 4: Verdict Output Format

```
📋 Ambiguity Check Result: [✅ Implementable / ⚠️ Conditionally OK / ❌ Not Ready]
Risk Level: [Critical / Standard / Low]

✅ Error Scenarios: 3 retries on timeout, return 503 on failure
✅ Input Constraints: Username 2–20 chars, alphanumeric + Unicode
⚠️ Auth/Permission: Default applied — own data access only (confirmation needed)
✅ Business Logic: 3 Given-When-Then scenarios confirmed
⏭️ Performance: Small scale — skipped
⏭️ Idempotency: Standard risk level — skipped

Unresolved Questions:
1. Can a User view another User's profile? (A/B/C)
```

## Constraints
- Use **multiple-choice questions** (A/B/C) instead of open-ended questions ("How should we handle this?")
- Even at Low risk, Business Logic (item 4) must always be checked
- If the user says "skip" or "later", mark the item as ⏭️ and proceed
- Maximum 3 questions at a time (prevent question overload)
- 3 questions → answers → follow-up questions → answers → ... loop
