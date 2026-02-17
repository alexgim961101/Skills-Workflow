---
name: logic-audit
description: |
  설계 초안에 대한 논리적 결함 검증 시뮬레이션.
  상태 머신 불법 전이, 동시성 문제, 데이터 정합성 위반, 레이스 컨디션을 검사.
  트리거: "설계 리뷰", "스펙 검증", "API 설계 검토", "로직 확인"
---

# Logic Audit (Mental Sandbox Simulation)

## Goal
설계 초안의 논리적 결함을 사전에 발견하고 세이프가드를 제안합니다.
설계가 코드로 전환되기 전에 "머릿속 시뮬레이션"으로 결함을 잡아냅니다.

## Instructions

### Step 0: 설계 요약 정리
검증을 시작하기 전에, 현재 설계에서 아래 요소를 추출합니다:

- **Entities**: 어떤 데이터 모델/테이블이 관련되는가?
- **State 유무**: 상태를 가진 엔티티가 있는가? (주문 상태, 사용자 상태 등)
- **공유 자원**: 여러 요청이 동시에 접근하는 데이터가 있는가?
- **외부 의존성**: 외부 API, 메시지 큐, 3rd-party 서비스가 있는가?
- **Side Effects**: 알림, 이메일, 결제, 이벤트 발행 등 되돌리기 어려운 작업이 있는가?

이 목록이 이후 4개 검증의 입력이 됩니다.

---

### (1) State Machine 검증

상태를 가진 엔티티가 있을 때 수행합니다.

#### 체크리스트
- [ ] **모든 상태 나열**: 가능한 모든 상태를 빠짐없이 정의했는가?
- [ ] **불법 전이 검사**: 각 상태에서 "갈 수 없는 상태"를 명시했는가?
- [ ] **종료 경로**: 모든 상태에서 최종 상태(terminal state)로 가는 경로가 존재하는가?
- [ ] **초기 상태**: 엔티티 생성 시 어떤 상태에서 시작하는가?
- [ ] **동시 전이**: 같은 엔티티에 대해 두 전이가 동시에 요청되면?
- [ ] **전이 조건**: 각 전이에 필요한 선행 조건(guard)이 명시되었는가?
- [ ] **전이 부수효과**: 상태 전이 시 발생하는 side effect가 정의되었는가?

#### 검증 방법: 전이 매트릭스 작성
설계에서 추출한 상태로 아래 매트릭스를 만듭니다.
✅ = 허용, ❌ = 불법, 🔒 = 조건부 (guard 필요)

```
        → Pending  Active  Paused  Completed  Cancelled
Pending     -       ✅      ❌       ❌        ✅
Active      ❌       -      ✅       ✅        ✅
Paused      ❌      ✅       -       ❌        ✅
Completed   ❌      ❌      ❌        -        ❌
Cancelled   ❌      ❌      ❌       ❌         -
```

#### 결함 패턴 (자주 발생하는 실수)
| 패턴 | 문제 | 해결 |
|------|------|------|
| **좀비 상태** | 어떤 상태에서 나갈 수 없음 (deadlock) | 타임아웃 전이 또는 관리자 강제 전이 추가 |
| **부활 전이** | 종료 상태에서 활성 상태로 복귀 | terminal 상태 전이를 코드 레벨에서 차단 |
| **암묵적 상태** | 코드에서만 존재하고 명시적 정의 없음 | 모든 상태를 enum으로 정의 |
| **전이 누락** | A→B는 정의했지만 A→C 경로가 필요한데 없음 | 매트릭스로 모든 조합 검증 |
| **Side Effect 불일치** | 전이는 성공했는데 알림 발송 실패 | Saga/Outbox 패턴 또는 보상 트랜잭션 |

#### 출력 형식
```
🔍 State Machine 검증: {엔티티명}
상태: [Pending, Active, Paused, Completed, Cancelled]

⚠️ [Med] 좀비 상태 발견
- 위치: Paused 상태
- 문제: Paused에서 Completed로 직접 전이 불가. 사용자가 Pause 후 완료하려면
        반드시 Active를 거쳐야 하는데, 이것이 의도된 것인가?
- 제안 A: Paused → Completed 전이 추가 (guard: 진행률 100%)
- 제안 B: 의도된 제약이면 UI에서 안내 메시지 표시
- 의도 확인 필요 ← 사용자에게 질문
```

---

### (2) Concurrency 검증

공유 자원이 존재할 때 수행합니다.

#### 체크리스트
- [ ] **동시 쓰기**: 두 사용자가 같은 레코드를 동시에 수정하면?
- [ ] **동시 생성**: 같은 unique 키로 동시에 INSERT 하면?
- [ ] **읽기-수정-쓰기**: Read → Modify → Write 사이에 다른 요청이 끼어들면?
- [ ] **카운터/잔액**: 동시 차감 시 마이너스로 갈 수 있는가?
- [ ] **분산 환경**: 서버 인스턴스가 여러 개일 때도 안전한가?

#### 검증 방법: 2-Request Simulation
가장 위험한 API를 골라서, **같은 밀리초에 2개 요청**이 도착하는 시나리오를 시뮬레이션합니다.

```
시간 →  T0          T1          T2          T3
────────────────────────────────────────────────
Req A:  READ(stock=5)  ---         WRITE(stock=4)  ---
Req B:  ---         READ(stock=5)  ---         WRITE(stock=4)
                                                ↑ 문제! 실제로는 3이어야 함
```

#### 동시성 해결 전략 선택 가이드

| 상황 | 전략 | 구현 | 트레이드오프 |
|------|------|------|-------------|
| 충돌 빈도 낮음, 성능 중요 | **Optimistic Lock** | version 컬럼 + WHERE version = X | 충돌 시 재시도 필요 |
| 충돌 빈도 높음, 정합성 중요 | **Pessimistic Lock** | SELECT ... FOR UPDATE | 성능 저하, 데드락 위험 |
| 분산 환경, 여러 서버 | **분산 Lock** | Redis SETNX, Redlock | 인프라 의존성, 장애 전파 |
| 멱등 처리 필요 | **Idempotency Key** | unique key + upsert | 클라이언트 협력 필요 |
| 숫자 증감 | **Atomic Operation** | UPDATE SET x = x - 1 WHERE x > 0 | DB 레벨 원자성만 보장 |
| 이벤트 순서 중요 | **Optimistic Lock + Queue** | version check → 실패 시 큐 재시도 | 복잡도 증가 |

#### 출력 형식
```
🔍 Concurrency 검증: {API명}

⚠️ [High] Lost Update 가능
- 위치: PUT /orders/:id/status
- 시뮬레이션:
  Req A: READ(status=pending) → WRITE(status=active)
  Req B: READ(status=pending) → WRITE(status=cancelled)
  → 마지막 쓰기가 이기는 Last-Write-Wins 문제
- 제안: Optimistic Lock
  - orders 테이블에 `version INT DEFAULT 0` 추가
  - UPDATE orders SET status = ?, version = version + 1
    WHERE id = ? AND version = ?
  - 영향 행 0이면 409 Conflict 반환
```

---

### (3) Data Integrity 검증

엔티티 간 관계가 존재할 때 수행합니다.

#### 체크리스트
- [ ] **Orphan 체크**: 부모 삭제 시 자식은 어떻게 되는가?
- [ ] **참조 무결성**: FK 제약이 필요한 곳에 빠짐없이 설정되었는가?
- [ ] **Soft Delete 파급**: soft delete 시 연관 엔티티의 조회에 영향은?
- [ ] **Cascade 범위**: CASCADE DELETE가 의도치 않게 넓은 범위를 삭제하지 않는가?
- [ ] **Nullable 관계**: 관계의 한쪽이 null이 될 수 있는가? (optional relation)
- [ ] **순환 참조**: A → B → C → A 같은 순환이 존재하는가?
- [ ] **다대다 관계**: 중간 테이블의 unique 제약이 올바른가?

#### 검증 방법: 삭제 시나리오 시뮬레이션
각 엔티티에 대해 "이 엔티티가 삭제되면 나머지는 어떻게 되는가?"를 추적합니다.

```
User 삭제 시:
├── User의 Orders → ?
│   ├── A) CASCADE: 주문도 삭제 (위험! 회계 데이터 유실)
│   ├── B) SET NULL: order.user_id = NULL (익명 주문화)
│   ├── C) RESTRICT: 주문 있으면 삭제 불가
│   └── D) SOFT DELETE: user.deleted_at 설정, 주문은 유지
│
├── User의 Comments → ?
│   ├── A) CASCADE: 댓글도 삭제
│   └── B) SET NULL: "탈퇴한 사용자" 표시
│
└── User의 Sessions → ?
    └── A) CASCADE: 즉시 전체 세션 무효화
```

#### 자주 발생하는 정합성 문제

| 패턴 | 문제 | 해결 |
|------|------|------|
| **Cascade 폭탄** | User 삭제 시 수천 개 연관 레코드 동시 삭제 → 트랜잭션 타임아웃 | Soft delete + 백그라운드 정리 |
| **Orphan 레코드** | FK 없이 application-level 참조 → 부모 삭제 후 고아 데이터 | FK 제약 추가 또는 정기 정리 Job |
| **Phantom Read** | 트랜잭션 중 다른 트랜잭션이 삽입 → 합계 불일치 | Serializable isolation 또는 명시적 Lock |
| **Soft Delete 유령** | deleted_at 필터 누락 → 삭제된 데이터가 조회됨 | Global scope/middleware에서 자동 필터 |
| **다대다 중복** | unique 제약 없이 동일 관계 중복 삽입 | composite unique index |

#### 출력 형식
```
🔍 Data Integrity 검증: {엔티티명} 삭제 시

⚠️ [High] Cascade 폭탄 위험
- 위치: User 삭제 시 Orders CASCADE
- 문제: 주문 1만 건 보유 사용자 삭제 시 트랜잭션 타임아웃 가능
        + 회계 데이터 영구 유실
- 제안: User는 Soft Delete Only
  - users.deleted_at 설정
  - 연관 Orders는 유지, user 정보는 "탈퇴한 사용자"로 표시
  - 30일 후 배치 Job으로 개인정보 익명화
```

---

### (4) Race Condition 검증

공유 자원 + 시간차 접근이 존재할 때 수행합니다.

#### 체크리스트
- [ ] **Check-then-Act**: 조건 확인 후 행동하는 패턴에서, 조건과 행동 사이에 간극이 있는가?
- [ ] **Double Spend**: 잔액/포인트/재고를 확인 후 차감하는 사이에 다른 차감이 가능한가?
- [ ] **TOCTOU**: Time Of Check vs Time Of Use 불일치
- [ ] **Pub/Sub 순서**: 이벤트 발행과 소비 사이에 순서가 보장되는가?
- [ ] **Cache Invalidation**: 캐시 갱신과 DB 갱신 사이의 정합성
- [ ] **파일/리소스 경쟁**: 같은 파일/S3 키에 동시 쓰기

#### 검증 방법: Timeline 다이어그램
의심되는 경로를 시간순으로 나열하여 검증합니다.

```
시나리오: 쿠폰 사용 (잔여 1장)

시간 →  T0              T1              T2              T3
───────────────────────────────────────────────────────────
Req A:  CHECK(남은=1)   ---             USE(남은=0) ✅   ---
Req B:  ---             CHECK(남은=1)   ---             USE(남은=-1) 💥
                        ↑ 아직 A가 USE 안 했으므로 1로 보임

결과: 쿠폰 1장인데 2명이 사용 → Double Spend
```

#### Race Condition 해결 패턴

| 패턴 | 적용 상황 | 구현 |
|------|----------|------|
| **Atomic Check-and-Set** | 단순 숫자 증감 | `UPDATE SET qty = qty - 1 WHERE qty > 0` + affected rows 체크 |
| **Pessimistic Lock** | 복잡한 비즈니스 로직 | `SELECT ... FOR UPDATE` → 로직 → COMMIT |
| **Optimistic Lock** | 충돌이 드문 경우 | version 체크 → 실패 시 재시도 |
| **Redis Lock** | 분산 환경 | `SET key value NX EX 30` → 작업 → DEL key |
| **Unique Constraint** | 중복 생성 방지 | DB unique index + ON CONFLICT 처리 |
| **Outbox Pattern** | DB 쓰기 + 이벤트 발행 원자성 | 같은 트랜잭션에 outbox 테이블 삽입 → 별도 폴러가 발행 |
| **Saga Pattern** | 분산 트랜잭션 | 각 서비스 로컬 트랜잭션 + 보상 트랜잭션 |

#### 출력 형식
```
🔍 Race Condition 검증: {기능명}

⚠️ [Critical] Double Spend 가능
- 위치: POST /coupons/:id/use
- 시나리오:
  T0: Req A → CHECK(remaining=1) → OK
  T1: Req B → CHECK(remaining=1) → OK (아직 A 미반영)
  T2: Req A → USE → remaining=0
  T3: Req B → USE → remaining=-1 💥
- 제안: Atomic Check-and-Set
  ```sql
  UPDATE coupons
  SET remaining = remaining - 1
  WHERE id = ? AND remaining > 0
  ```
  affected_rows = 0 이면 → 409 "쿠폰이 이미 소진되었습니다"
```

---

### Step Final: 종합 리스크 보고서

모든 검증을 마친 후, 아래 형식으로 종합 보고합니다:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔍 Logic Audit 완료
━━━━━━━━━━━━━━━━━━━━━━━━━━━━

검증 요약:
  (1) State Machine:  ✅ 통과 / ⚠️ N건 발견
  (2) Concurrency:    ✅ 통과 / ⚠️ N건 발견
  (3) Data Integrity: ✅ 통과 / ⚠️ N건 발견
  (4) Race Condition: ✅ 통과 / ⚠️ N건 발견

발견 사항:
  🔴 Critical: N건
  🟡 High: N건
  🟢 Med: N건
  ⚪ Low: N건

적용한 세이프가드:
  1. {엔티티} — Optimistic Lock (version 필드)
  2. {API} — Atomic Check-and-Set (WHERE qty > 0)
  3. {삭제} — Soft Delete Only + 30일 배치 정리

⚠️ 사용자 확인 필요:
  1. Paused → Completed 직접 전이를 허용할까요? (A/B)
  2. User 삭제 정책: Soft Delete vs Hard Delete? (A/B/C)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Constraints
- 4개 검증 중 해당 없는 것은 "해당 없음 (상태 없는 설계)" 으로 스킵
- 결함 발견 시 반드시 **구체적 수정안 + 코드/SQL 레벨 제안**을 함께 제시
- 위험도: Critical > High > Med > Low
  - **Critical**: 데이터 유실 또는 금전적 손해 가능
  - **High**: 데이터 불일치 발생 가능
  - **Med**: 엣지케이스에서만 문제 가능
  - **Low**: 코드 품질/유지보수성 이슈
- 수정안은 사용자 확인 없이 적용하지 않음
- Critical/High는 설계에 반드시 반영 후 진행, Med/Low는 기록 후 진행 가능
