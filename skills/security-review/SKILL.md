---
name: security-review
description: |
  Skill for checking security vulnerabilities in changed code based on OWASP.
  Used for code reviews, QA, and external input handling code changes.
  Triggers: "security check", "security review", "vulnerability check"
---

# Security Review

## Goal
Verify that changed code has no security vulnerabilities and
provide specific remediation recommendations.

## Instructions

### Step 1: Determine Security Scope

Check whether the changed code falls into the areas below.
**Only inspect applicable areas** (prevent unnecessary checks).

| Area | Applicable when | Check target |
|------|-----------------|-------------|
| **Input handling** | Code that accepts user/external input | Step 2 |
| **Auth/AuthZ** | Login, permissions, tokens, sessions | Step 3 |
| **Data protection** | Personal data, sensitive data, secrets | Step 4 |
| **Infrastructure/Config** | Docker, env variables, CORS, headers | Step 5 |

### Step 2: Input Validation (Injection)

- [ ] **SQL Injection**: Is the query built by string concatenation? → Use Parameterized Query
- [ ] **XSS**: Is user input rendered as-is in HTML? → Escape/sanitize
- [ ] **Command Injection**: Is external input included in OS commands?
- [ ] **Path Traversal**: Is user input used directly in file paths?
- [ ] **Deserialization**: Is untrusted data being deserialized?
- [ ] **Validation location**: Is validation done server-side? (client-only is insufficient)

### Step 3: Authentication/Authorization (Auth)

- [ ] **Auth bypass**: Are there endpoints accessible without authentication?
- [ ] **Permission check**: Is owner/permission verified on resource access? (IDOR prevention)
- [ ] **Token management**: Are JWT signature validation, expiry, refresh token management adequate?
- [ ] **Passwords**: Stored with proper hashing (bcrypt/argon2), not plaintext?
- [ ] **Session management**: Are session fixation and session hijacking defenses in place?

### Step 4: Data Protection (Secrets & PII)

- [ ] **Hardcoded secrets**: Are API keys, passwords, tokens not hardcoded in source?
- [ ] **Log exposure**: Are passwords, tokens, PII not printed in logs?
- [ ] **Error messages**: Are stack traces or internal paths not exposed to users?
- [ ] **Transport security**: Is sensitive data transmitted over HTTPS?
- [ ] **Storage security**: Is sensitive data encrypted at rest?

### Step 5: Infrastructure/Configuration

- [ ] **CORS**: Is the allowed Origin not set to `*`?
- [ ] **Security headers**: Are `X-Content-Type-Options`, `X-Frame-Options`, CSP configured?
- [ ] **Exposed ports**: Are debug ports, DB ports not exposed externally?
- [ ] **Dependency vulnerabilities**: Are any libraries with known CVEs in use?

### Step 6: Report Results

```
🔒 Security Review

Reviewed areas: [Input handling / Auth / Data protection / Infra] applicable areas

🔴 Critical (Must fix immediately):
  - [file:line] Vulnerability type: Description → Remediation

🟠 Warning (Fix recommended):
  - [file:line] Vulnerability type: Description → Remediation

✅ Review complete: No findings / N finding(s)

Verdict: ✅ PASS / ❌ FAIL (FAIL if 🔴 Critical ≥ 1)
```

## Constraints
- Only inspect applicable areas — skip areas unrelated to the change
- 🔴 Critical even 1 means FAIL → re-inspect after fix
- Apply security fixes after functional testing (ensure security patches don't break functionality)
