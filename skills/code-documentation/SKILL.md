---
name: code-documentation
description: |
  코드와 문서를 동기화하는 스킬. 인코드 문서, API 문서, README를 최신 상태로 유지.
  구현 완료 후, API 변경 후, 문서 업데이트가 필요할 때 사용.
  트리거: "문서 업데이트", "API 문서", "README 갱신", "docs sync"
---

# Code Documentation

## Goal
코드와 문서 사이의 불일치(drift)를 제거하고,
Single Source of Truth 원칙에 따라 문서를 최신 상태로 유지합니다.

## Instructions

### Step 1: 변경 범위 파악

- 변경된 파일 목록 확인
- 각 파일에서 public API(함수, 클래스, 메서드)가 변경되었는지 확인
- 관련 문서 파일(README, CHANGELOG, API spec 등) 식별

### Step 2: 인코드 문서 (In-Code Documentation)

변경된 파일의 public 인터페이스에 대해 주석을 추가/업데이트합니다.

| 언어 | 형식 | 필수 태그 |
|------|------|----------|
| Java | Javadoc | `@param`, `@return`, `@throws` |
| JS/TS | JSDoc | `@param`, `@returns`, `@example` |
| Python | Docstring | Args, Returns, Raises |
| Go | GoDoc | 함수명으로 시작하는 주석 |
| Rust | `///` | 예시 포함 권장 |

**규칙:**
- public 인터페이스에만 주석 (private 구현은 코드가 스스로 설명)
- 주석은 "무엇"과 "왜"를 설명, "어떻게"는 코드로
- 기존 주석이 있으면 변경에 맞게 업데이트

### Step 3: API 문서

API 변경이 있으면:
- [ ] Swagger/OpenAPI 정의 업데이트
- [ ] 엔드포인트, 파라미터, 응답 형식이 실제 코드와 일치하는지 확인
- [ ] 새 엔드포인트에 대해 요청/응답 예시 추가

### Step 4: 프로젝트 문서

아키텍처 변경이 있으면:
- [ ] README의 설치/실행/빌드 방법이 여전히 유효한지 확인
- [ ] CHANGELOG에 주요 변경 사항 추가 (있는 경우)
- [ ] 아키텍처 다이어그램 업데이트 (있는 경우)

### Step 5: Drift 검증

최종적으로 코드 ↔ 문서 간 불일치가 없는지 확인합니다.

```
📚 Documentation Sync Report

인코드 문서: N개 파일 업데이트
API 문서: [업데이트됨 / 변경 없음 / 해당 없음]
README: [업데이트됨 / 변경 없음]
CHANGELOG: [업데이트됨 / 변경 없음 / 해당 없음]

Drift 검출: ✅ 없음 / ⚠️ N건
```

## Constraints
- 문서는 코드를 반영 — 코드가 변경되면 문서도 반드시 따름
- 존재하지 않는 문서 파일을 무조건 생성하지 않음 — 프로젝트에 해당 문서가 있을 때만 업데이트
- 과도한 주석 자제 — 자명한 코드에는 주석 불필요
