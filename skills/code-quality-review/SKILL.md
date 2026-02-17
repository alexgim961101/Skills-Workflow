---
name: code-quality-review
description: |
  변경된 코드의 품질을 SOLID, 효율성, 리팩터링 관점에서 심층 점검하는 스킬.
  코드 리뷰, PR 점검, QA 품질 게이트, 문서 동기화 검증 시 사용.
  트리거: "코드 리뷰", "품질 점검", "code review", "SOLID 점검", "리팩터링 점검"
---

# Code Quality Review

## Goal
변경된 코드가 품질 기준을 충족하는지 점검하고,
**비효율적 코드와 리팩터링이 필요한 부분까지** 구체적인 개선 방안을 제안합니다.

## Instructions

### Step 1: 점검 범위 결정

- 변경된 파일 목록 확인 (diff 기반)
- 변경된 파일만 점검 (전체 코드베이스 리뷰가 아님)

### Step 2: 설계 품질 점검

**SOLID 원칙:**
- [ ] **SRP**: 하나의 클래스/함수가 하나의 책임만 갖는가?
- [ ] **OCP**: 기존 코드를 수정하지 않고 확장 가능한 구조인가?
- [ ] **LSP**: 상속/구현이 치환 가능한가?
- [ ] **ISP**: 불필요한 인터페이스 의존이 없는가?
- [ ] **DIP**: 상위 모듈이 하위 모듈 구현체에 직접 의존하지 않는가?

**Clean Architecture:**
- [ ] 도메인 로직이 프레임워크/UI/DB에 독립적인가?
- [ ] 의존성 방향이 안쪽(도메인)으로 향하는가?
- [ ] 계층 간 경계가 명확한가?

### Step 3: 효율성 점검

**데이터 접근:**
- [ ] **N+1 쿼리**: 루프 안에서 개별 쿼리를 호출하고 있지 않은가?
- [ ] **불필요한 전체 조회**: 필요한 컬럼/행만 가져오는가?
- [ ] **인덱스 누락**: WHERE/JOIN 조건에 인덱스가 필요하지 않은가?
- [ ] **캐시 활용**: 반복적으로 동일한 데이터를 조회하고 있지 않은가?

**알고리즘/로직:**
- [ ] **불필요한 반복**: 중첩 루프를 단순화할 수 있는가? (O(n²) → O(n))
- [ ] **과도한 객체 생성**: 루프 안에서 불필요한 인스턴스를 생성하지 않는가?
- [ ] **조기 반환 누락**: 불필요한 연산을 조기 반환으로 피할 수 있는가?
- [ ] **스트림/컬렉션 남용**: 다중 스트림 체인을 단일 패스로 합칠 수 있는가?

**리소스 관리:**
- [ ] **연결/스트림 미해제**: try-with-resources, defer, using 등으로 보호되는가?
- [ ] **메모리 누수 패턴**: 이벤트 리스너, 콜백, 캐시가 적절히 정리되는가?

### Step 4: 코드 스멜 & 리팩터링 기회 탐지

아래 패턴이 발견되면 구체적 리팩터링 방안을 제안합니다.

| 코드 스멜 | 탐지 기준 | 리팩터링 제안 |
|-----------|----------|-------------|
| **God Object** | 클래스가 5+개 책임, 300+줄 | 책임별 클래스 분리 |
| **Feature Envy** | 다른 객체의 데이터를 과도하게 참조 | 로직을 데이터 소유 객체로 이동 |
| **Long Method** | 함수 30+줄 또는 분기 5+개 | Extract Method로 분해 |
| **Primitive Obsession** | 원시 타입으로 도메인 개념 표현 | Value Object 도입 |
| **Shotgun Surgery** | 하나의 변경이 여러 클래스에 산탄처럼 퍼짐 | 관련 로직을 하나의 모듈로 응집 |
| **Duplicated Logic** | 유사 코드 블록 2+곳에 존재 | 공통 함수/유틸로 추출 |
| **Magic Number/String** | 상수 없이 리터럴 값 직접 사용 | Named Constant 또는 Enum 추출 |
| **Deep Nesting** | if/for 3단계 이상 중첩 | Guard Clause, Early Return, 분리 |
| **Boolean Parameter** | 함수가 boolean으로 동작을 분기 | 별도 함수로 분리 또는 Strategy |

### Step 5: 문서 동기화 확인

변경에 따라 문서 갱신이 필요한지 확인합니다.

- [ ] 변경된 public API에 Javadoc/JSDoc/Docstring이 반영되었는가?
- [ ] API 시그니처 변경 시 Swagger/OpenAPI가 업데이트되었는가?
- [ ] README의 설치/실행 방법이 여전히 유효한가?

> 문서 자체를 작성하는 것이 아니라, **누락 여부만 지적**합니다.

### Step 6: 심각도 분류 및 보고서

| 심각도 | 기준 | 대응 |
|--------|------|------|
| **🔴 Blocker** | 런타임 에러, 보안 취약점, 데이터 손실 가능 | **반드시 수정** → FAIL |
| **🟠 Refactor** | 효율성 문제, 코드 스멜, 리팩터링 필요 | **수정 필수** → FAIL |
| **🟡 Warning** | SOLID 위반, 높은 결합도, 복잡한 분기 | 수정 권장 |
| **🔵 Info** | 네이밍 개선, 코드 스타일, 미세 최적화 | 참고 사항 |

```
📋 Code Quality Review

점검 파일: N개
발견 사항: 🔴 X건 / 🟠 Y건 / 🟡 Z건 / 🔵 W건
판정: ✅ PASS / ❌ FAIL (🔴 또는 🟠 존재 시)
문서 동기화: ✅ 정상 / ⚠️ N건 누락

[심각도순으로 나열]

🔴 [Blocker] 리소스 누수 — ConnectionPool.java
   - 문제: DB 연결이 finally 블록 없이 사용
   - 제안: try-with-resources 적용

🟠 [Refactor] N+1 쿼리 — UserService.java:45
   - 문제: 루프 안에서 findById() 개별 호출 (100명 → 100쿼리)
   - 제안: findAllById() 배치 조회로 변경
   - 성능 영향: ~100x 쿼리 감소

🟠 [Refactor] God Object — OrderService.java
   - 문제: 주문 생성, 결제, 알림, 재고 관리를 한 클래스에서 처리 (450줄)
   - 제안: OrderCreationService, PaymentService, NotificationService로 분리
   - 패턴: Facade → 개별 서비스 위임

🟡 [Warning] DIP 위반 — OrderController.java
   - 문제: OrderRepositoryImpl에 직접 의존
   - 제안: OrderRepository 인터페이스를 통해 주입

⚠️ [Docs] 문서 미동기화 — UserController.java
   - 문제: 새 엔드포인트 POST /users/bulk 추가됐으나 API 문서 미반영
```

**판정 규칙:**
- 🔴 Blocker 또는 🟠 Refactor가 **1건이라도 있으면 → ❌ FAIL**
- 🟡 Warning만 있으면 → ✅ PASS (권장 사항으로 기록)
- 🔵 Info만 있으면 → ✅ PASS

## Constraints
- 단순 불평 대신 **구체적인 디자인 패턴/해결책을 제안**
- 성능 문제는 **정량적 영향**(쿼리 수, 시간 복잡도 등)을 명시
- 프로젝트의 기존 스타일과 아키텍처를 존중 (급진적 리팩터링 제안 자제)
- 🟠 Refactor는 **명확한 비효율/스멜이 있을 때만** — 주관적 선호가 아닌 객관적 기준 적용
