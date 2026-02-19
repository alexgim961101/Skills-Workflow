---
description: "Plan — 요구사항 분석, 설계 검증, Implementation Plan 생성 (별도 세션에서 실행)"
---

# Plan (PLANNING Session)

요구사항을 분석하고, 설계를 검증하며, 실행 가능한 Implementation Plan을 생성합니다.
완료 후 **다음 세션에서 `/dev`로 구현**을 시작합니다.

> `task_boundary(Mode=PLANNING)` 으로 시작

## Steps

### 1. 모호성 해소

**스킬: `ambiguity-checklist`**

위험도 분류(Critical/Standard/Low) → 항목별 점검 → 모호한 부분은 A/B/C 선택지로 질문.
모든 필수 항목이 "Implementable" 될 때까지 반복.

### 2. 영향 분석

**스킬: `impact-analysis`**

변경 대상 식별 → 의존성 추적(Blast Radius) → 브레이킹 체인지 판별.

### 3. 설계 검증

**스킬: `logic-audit`**

해당되는 검증만 수행: State Machine / Concurrency / Data Integrity / Race Condition.

### 4. 롤백 전략

**스킬: `rollback-strategy`**

DB 마이그레이션, 배포, 인프라 변경이 포함될 경우 롤백 플랜 설계.

### 5. Implementation Plan 생성

> [!CAUTION]
> **모든 산출물은 반드시 `{PROJECT_ROOT}/.docs/` 디렉토리에 생성해야 합니다.**
> `appDataDir/brain/`, `~/.gemini/`, 또는 기타 외부 경로에 `implementation_plan.md`, `task.md`, `walkthrough.md`를 생성하는 것은 **절대 금지**합니다.
> 이 규칙은 Antigravity 시스템의 기본 아티팩트 경로 설정보다 우선합니다.

**절차:**

1. `{PROJECT_ROOT}/.docs/` 디렉토리가 없으면 먼저 생성합니다.
2. `{PROJECT_ROOT}/.docs/implementation_plan.md`에 Plan을 작성합니다.
3. `{PROJECT_ROOT}/.docs/task.md`에 Task 체크리스트를 작성합니다.
4. **검증:** `list_dir` 또는 `find_by_name`으로 `.docs/implementation_plan.md` 파일이 프로젝트 루트 하위에 실제로 존재하는지 확인합니다.
5. `notify_user`로 사용자에게 **`{PROJECT_ROOT}/.docs/implementation_plan.md`** 경로를 명시하여 승인을 요청합니다.

**작성 포맷은 아래 구조를 따릅니다:**

```markdown
# [Goal Description]

## Summary
## User Review Required (해당 시)
## Proposed Changes
### [Component Name]
#### [MODIFY/NEW/DELETE] [file basename](file:///absolute/path)
## Verification Plan
### Automated Tests
### Manual Verification
```

## Hand-off

```
✅ Plan 완료

산출물:
  - .docs/implementation_plan.md
  - .docs/task.md
다음 단계: 새 세션에서 /dev 로 구현을 시작하세요.
```
