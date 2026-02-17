---
name: incremental-execution
description: |
  승인된 Plan을 작은 기능 단위(Chunk)로 나누어 구현하고,
  각 Chunk마다 사용자 코드 리뷰를 받는 점진적 개발 스킬.
  트리거: "단계별 구현", "plan 실행", "적용 시작", "구현 시작"
---

# Incremental Execution

## Goal
승인된 Implementation Plan을 작은 기능 단위로 점진적으로 구현합니다.
**한 번에 전부 만들지 않고**, 각 단위마다 사용자의 코드 리뷰와 승인을 받은 후 다음으로 진행합니다.

## Instructions

### Step 0: 프로젝트 환경 탐지 (Build Tool Detection)

구현을 시작하기 전에, 코드를 검증할 명령어를 자동으로 찾습니다.

| 언어/환경 | 탐지 대상 | 검증 명령 |
|-----------|----------|----------|
| Java | `gradlew`, `mvnw` | `./gradlew build`, `./mvnw verify` |
| JS/TS | `package.json` scripts | `npm run lint`, `npm run build` |
| Python | `pytest`, `ruff`, `Makefile` | `pytest`, `ruff check` |
| Go | `go.mod` | `go vet ./...`, `go build ./...` |
| Rust | `Cargo.toml` | `cargo check`, `cargo clippy` |
| 공통 | `Makefile`, `Justfile` | `make`, `just` |

탐지 결과를 `{VERIFY_CMD}`로 정의합니다.
찾을 수 없으면 사용자에게 물어봅니다.

---

### Step 1: Chunk 분할

Plan의 Proposed Changes를 **작은 기능 단위(Chunk)**로 분할합니다.

**Chunk 분할 원칙:**
- 각 Chunk는 독립적으로 빌드/테스트 가능
- 각 Chunk는 사용자가 10분 내에 리뷰할 수 있는 크기
- 의존성 순서: 하위 레이어(모델/인터페이스) → 상위 레이어(서비스/API)

**Chunk 목록 출력 예시:**
```
📦 구현 Chunk 목록 (총 4개)

Chunk 1/4: 데이터 모델 정의
  - 변경 파일: User.java, UserDTO.java
  - 예상 크기: ~50줄

Chunk 2/4: Repository 레이어
  - 변경 파일: UserRepository.java
  - 의존: Chunk 1
  - 예상 크기: ~30줄

Chunk 3/4: Service 비즈니스 로직
  - 변경 파일: UserService.java
  - 의존: Chunk 1, 2
  - 예상 크기: ~80줄

Chunk 4/4: Controller + API 연결
  - 변경 파일: UserController.java
  - 의존: Chunk 1, 2, 3
  - 예상 크기: ~40줄
```

---

### Step 2: Chunk 구현 → 리뷰 루프 (핵심)

각 Chunk에 대해 아래 사이클을 반복합니다.

```
┌─────────────────────────────────────────┐
│  Chunk N 구현                           │
│  ↓                                      │
│  {VERIFY_CMD} 실행 (빌드/테스트)         │
│  ↓                                      │
│  ✅ 통과 → 사용자에게 리뷰 요청          │
│  ↓                                      │
│  사용자 응답 대기                        │
│  ├─ "승인" → Chunk N+1로 진행           │
│  ├─ "수정 요청" → 수정 후 재제출         │
│  └─ "중단" → 현재까지 결과 저장 후 종료  │
└─────────────────────────────────────────┘
```

**리뷰 요청 형식:**
```
📝 Chunk {N}/{Total} 리뷰 요청: {Chunk 제목}

변경 파일:
  - [파일명] 변경 내용 한 줄 요약

빌드/테스트: ✅ 통과

다음 Chunk: {N+1} — {다음 Chunk 제목}

→ 승인, 수정 요청, 또는 중단을 선택해주세요.
```

**핵심 규칙:**
1. **절대 다음 Chunk로 먼저 넘어가지 않는다** — 사용자 승인 필수
2. 수정 요청을 받으면, 수정 후 다시 검증 + 리뷰 요청
3. Chunk 구현 중 Plan에서 벗어나야 하면, 먼저 사용자에게 알리고 Plan 갱신

---

### Step 3: 최종 통합 검증

모든 Chunk가 승인되면:

1. 전체 `{VERIFY_CMD}` 실행 (통합 빌드/테스트)
2. 결과 보고

```
✅ 전체 구현 완료

구현된 Chunk: {N}/{N}
전체 빌드: ✅ 통과
전체 테스트: ✅ 통과 (X개 중 X개 성공)

→ walkthrough-generation 스킬로 결과를 정리할 수 있습니다.
```

## Constraints
- 한 번에 모든 코드를 작성하지 않음 — 반드시 Chunk 단위로 분할
- 각 Chunk는 사용자 리뷰 없이 다음으로 넘어가지 않음
- `{VERIFY_CMD}`가 실패하면 사용자에게 리뷰 요청하지 않고 먼저 수정
- 테스트 코드 작성이 필요한 경우 해당 Chunk에 포함
