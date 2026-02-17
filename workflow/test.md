---
description: "Test — 테스트 전략 수립, 테스트 작성/실행, 테스트 품질 검증 (별도 세션에서 실행)"
---

# Test (VERIFICATION Session 1)

변경된 코드에 대해 테스트를 작성·실행하고, 테스트 자체의 품질까지 검증합니다.
**`/dev` 세션 이후, `/review` 세션 이전에 실행합니다.**

> `task_boundary(Mode=VERIFICATION)` 으로 시작

## Pre-Flight

1. `.docs/implementation_plan.md`에서 변경 범위 확인.
2. 변경된 파일 목록 파악.
3. `{VERIFY_CMD}` 확인 (빌드 도구).
4. **테스트 환경 탐지**:

| 점검 항목 | 탐지 방법 |
|----------|----------|
| 테스트 프레임워크 | `package.json`(jest/vitest), `build.gradle`(JUnit), `requirements.txt`(pytest) 등 |
| 설정 파일 | `jest.config.*`, `conftest.py`, `testcontainers.*` 등 |
| 테스트 디렉토리 | `src/test/`, `__tests__/`, `tests/` 등 |
| 테스트 유틸리티 | mock 라이브러리, 테스트 DB 설정, fixture 등 |

**환경이 갖춰져 있지 않은 경우:**
```
⚠️ 테스트 환경 미감지

누락 항목: [프레임워크 / 설정 파일 / 디렉토리]
제안 세팅:
  - [라이브러리 설치 명령]
  - [설정 파일 생성 내용]
  - [디렉토리 구조]

→ 이대로 세팅을 진행할까요?
```
**사용자 승인 후에만 세팅을 진행합니다. 자동으로 설치하지 않습니다.**

## Steps

**스킬: `test-strategy`**

1. **변경 유형 판별**: New / Update / Verify 모드 결정.
2. **테스트 작성/업데이트**: Happy Path + Edge Cases + Error Cases.
3. **실행**: 새 테스트 + 기존 회귀 테스트.
4. **테스트 품질 게이트**:
   - Assertion 품질 (Empty Test, Tautology)
   - Mock 품질 (과도한 Mock, 미검증 Mock)
   - 커버리지 갭 (Error Path, 경계값, 상태 전이)

**FAIL 시 → 테스트 수정 후 재실행. BUG/REGRESSION 발견 시 → `/dev` 세션으로 복귀.**

## Hand-off

```
🧪 Test 완료

새 테스트: N개, 수정: M개
실행 결과: ✅ 전체 통과
테스트 품질: ✅ Assertion OK / Mock OK / Coverage OK

다음 단계: 새 세션에서 /review 로 코드 리뷰를 시작하세요.
```
