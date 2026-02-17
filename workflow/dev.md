---
description: "Dev — 승인된 Plan을 Chunk 단위로 점진 구현, 빌드 검증, 코드 리뷰 (별도 세션에서 실행)"
---

# Dev (EXECUTION Session)

승인된 Implementation Plan을 Chunk 단위로 점진적으로 구현합니다.
**`.docs/implementation_plan.md`를 읽고 시작합니다.**

> `task_boundary(Mode=EXECUTION)` 으로 시작

## Pre-Flight

1. **Plan 로딩**: `.docs/implementation_plan.md`를 읽습니다.
2. **Build Tool Detection**: 프로젝트 루트에서 `{VERIFY_CMD}`를 자동 탐지합니다.

| 언어 | 탐지 | 검증 명령 |
|------|------|----------|
| Java | `gradlew`, `mvnw` | `./gradlew build` |
| JS/TS | `package.json` | `npm run build` |
| Python | `pytest` | `pytest` |
| Go | `go.mod` | `go build ./...` |
| Rust | `Cargo.toml` | `cargo check` |

## Chunk Loop (핵심)

### 1. Chunk 분할

Plan을 작은 기능 단위로 분할. 각 Chunk는 독립 빌드/테스트 가능.
`.docs/task.md`에 체크리스트로 기록.

### 2. Chunk 구현 → 리뷰 반복

```
각 Chunk마다:
  .docs/task.md [/] 마킹 → 코딩 → {VERIFY_CMD} 실행
  ├─ ❌ → 수정 후 재실행
  └─ ✅ → notify_user 리뷰 요청
       ├─ "승인" → [x] 마킹 → 다음 Chunk
       ├─ "수정 요청" → 반영 후 재제출
       └─ "중단" → 현재까지 저장 후 종료
```

**규칙:**
- 사용자 승인 없이 다음 Chunk로 넘어가지 않음
- 기존 프로젝트 스타일을 따름 ("Fit in")

### 3. 스펙 변경 프로토콜 (Mid-Flight Change)

| 규모 | 기준 | 대응 |
|------|------|------|
| **Minor** | 현재 Chunk에서 흡수 가능 | 바로 반영, 리뷰 시 사유 고지 |
| **Medium** | 이후 Chunk에 영향 | Plan 갱신 → 승인 → 재개 |
| **Major** | 전체 Plan 무효화 | 결과 기록 → 새 `/plan` 세션 시작 |

## Hand-off

```
✅ 구현 완료

구현된 Chunk: N/N
전체 빌드: ✅
다음 단계: 새 세션에서 /qa 로 품질 검증을 시작하세요.
```
