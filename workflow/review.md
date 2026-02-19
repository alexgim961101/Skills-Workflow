---
description: "Review — 코드 품질, 보안, AI 감사, 성능 심층 리뷰 (별도 세션에서 실행)"
---

# Review (VERIFICATION Session 2)

변경된 코드를 최대 6가지 관점에서 심층 리뷰합니다.
**`/test` 세션 이후에 실행합니다.**

> `task_boundary(Mode=VERIFICATION)` 으로 시작

## Pre-Flight

1. `.docs/implementation_plan.md`에서 변경 범위 확인.
2. 변경된 파일 목록 파악.
3. **프론트엔드 변경 감지**: `.tsx`, `.jsx`, `.vue`, `.svelte`, `.css`, `.scss`, `.html` 파일이 포함되어 있으면 Gate 5, 6 활성화.

## Review Pipeline (4 + 2 조건부 Gate)

**어느 Gate에서든 FAIL → `/dev` 세션으로 돌아가 수정합니다.**

### Gate 1: 코드 품질

**스킬: `code-quality-review`**

SOLID + 효율성 + 코드 스멜 9가지 + 문서 동기화.

### Gate 2: 보안

**스킬: `security-review`**

해당 영역만 점검: 입력 검증 / 인증·인가 / 데이터 보호 / 인프라·설정.

### Gate 3: AI 코드 감사

**스킬: `ai-code-audit`**

환각 API 검증 → 프로젝트 일관성 → 과잉 설계 탐지 → 완전성 검증.

### Gate 4: 성능

**스킬: `performance-review`**

해당 영역만 점검: 알고리즘 / DB / I·O / 메모리 / 동시성.

### Gate 5: UI/UX (프론트엔드 변경 시)

**스킬: `ui-ux-review`**

사용성 휴리스틱 + 시각적 일관성 + 인터랙션 패턴 + 반응형 디자인.

### Gate 6: 접근성 (프론트엔드 변경 시)

**스킬: `accessibility-review`**

WCAG 2.1 AA: 시맨틱 HTML + 키보드 내비게이션 + ARIA + 색상 대비.

## Final

전체 `{VERIFY_CMD}` 실행 (통합 빌드/테스트).
배포 포함 시 `health-check` 스킬 적용.
`.docs/walkthrough.md`에 결과 정리.

## Report

```
📋 Review 완료

Gate 1 (품질):    ✅ PASS — 🔴 0건, 🟠 0건
Gate 2 (보안):    ✅ PASS — 🔴 0건
Gate 3 (AI 감사): ✅ PASS — 환각 0건
Gate 4 (성능):    ✅ PASS — 🔴 0건
Gate 5 (UI/UX):   ✅ PASS — 또는 ⏭️ SKIP (백엔드만 변경)
Gate 6 (접근성):  ✅ PASS — 또는 ⏭️ SKIP (백엔드만 변경)
통합 빌드:        ✅ PASS

산출물: .docs/walkthrough.md
```
