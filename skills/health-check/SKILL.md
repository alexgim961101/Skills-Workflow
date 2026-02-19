---
name: health-check
description: |
  Skill for verifying system status after deployment or infrastructure changes, and determining rollback necessity.
  Used immediately after deployment, after infra changes, or when an incident is suspected.
  Triggers: "health check", "deployment verification", "post-deployment check"
---

# Health Check

## Goal
Verify that the system is operating normally after deployment or infrastructure changes, and
if issues are found, determine and suggest whether to rollback.

## Instructions

### Step 1: Confirm Change Information

- Summary of the last deployment/change (version, target service, timestamp)
- Reference the related Implementation Plan or change records

### Step 2: Execute Health Checks

**Endpoint checks:**
- [ ] Call health check endpoints (`/health`, `/ready`, `/live`, etc.)
- [ ] Verify HTTP status codes (expect 200 OK)
- [ ] Check response body for abnormal indicators

**Business path checks:**
- [ ] Execute core user flow scenarios (login, read, create, etc.)
- [ ] Check for error responses, timeouts, abnormal data

### Step 3: Verify Monitoring Metrics

| Metric | What to check | Abnormal criteria |
|--------|---------------|-------------------|
| Error rate | Compare before/after deployment | Spike in 5xx ratio |
| Response time | p50, p95, p99 | 2x+ increase vs. pre-deployment |
| Traffic | Request count trend | Sudden DROP |
| CPU/Memory | Resource usage | Sudden increase or OOM |
| DB connections | Connection pool usage | Signs of pool exhaustion |

### Step 4: Log Sampling

- Check error/warning logs from the last N minutes
- Identify new error patterns
- If recurring errors exist, summarize frequency and pattern

### Step 5: Verdict & Recommendation

**Result output format:**
```
🏥 Health Check Report

Deployment info: [Service name] v[Version] — [Timestamp]

Health endpoints: ✅ / ⚠️ / ❌
Business paths:  ✅ / ⚠️ / ❌
Monitoring:      ✅ / ⚠️ / ❌
Log status:      ✅ / ⚠️ / ❌

Overall verdict: ✅ Healthy / ⚠️ Needs monitoring / ❌ Rollback recommended

[If ❌]
Rollback procedure:
  1. ...
  2. ...
```

## Constraints
- If no automated monitoring tools are available, present manual check items to the user
- If the verdict is ambiguous, classify as "Needs monitoring" and suggest an additional observation period
- When recommending rollback, reference the Implementation Plan's Rollback Plan
