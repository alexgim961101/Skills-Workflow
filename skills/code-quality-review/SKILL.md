---
name: code-quality-review
description: |
  변경된 코드의 품질을 SOLID, Clean Architecture, Clean Code 관점에서 점검하는 스킬.
  코드 리뷰, PR 점검, 품질 게이트 시 사용.
  트리거: "코드 리뷰", "품질 점검", "code review", "SOLID 점검"
---

# Code Quality Review

## Goal
변경된 코드가 품질 기준을 충족하는지 점검하고,
구체적인 개선 방안을 제안합니다.

## Instructions

### Step 1: 점검 범위 결정

- 변경된 파일 목록 확인 (diff 기반)
- 변경된 파일만 점검 (전체 코드베이스 리뷰가 아님)

### Step 2: 품질 기준 점검

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

**Clean Code:**
- [ ] 함수/변수/클래스 이름이 의도를 명확히 전달하는가?
- [ ] 함수가 한 가지 일만 하며, 적절한 크기인가?
- [ ] 중복 코드가 없는가?
- [ ] 매직 넘버/스트링 대신 상수를 사용하는가?
- [ ] 에러 처리가 일관적인가?

### Step 3: 심각도 분류

| 심각도 | 기준 | 대응 |
|--------|------|------|
| **🔴 Blocker** | 런타임 에러, 보안 취약점, 데이터 손실 가능 | **반드시 수정** |
| **🟡 Warning** | SOLID 위반, 높은 결합도, 복잡한 분기 | 수정 권장 |
| **🔵 Info** | 네이밍 개선, 코드 스타일, 미세 최적화 | 참고 사항 |

### Step 4: 리뷰 보고서

```
📋 Code Quality Review

점검 파일: N개
발견 사항: 🔴 X건 / 🟡 Y건 / 🔵 Z건

[심각도순으로 나열]

🔴 [Blocker] SRP 위반 — UserService.java
   - 문제: 인증 + 프로필 + 알림 로직이 한 클래스에 혼재
   - 제안: AuthService, ProfileService, NotificationService로 분리
   - 패턴: Facade → 개별 서비스 위임

🟡 [Warning] DIP 위반 — OrderController.java
   - 문제: OrderRepositoryImpl에 직접 의존
   - 제안: OrderRepository 인터페이스를 통해 주입

🔵 [Info] 네이밍 — Utils.java:45
   - 문제: processData() — 무슨 데이터를 어떻게 처리?
   - 제안: sanitizeUserInput() 또는 transformToDTO()
```

## Constraints
- 단순 불평 대신 **구체적인 디자인 패턴/해결책을 제안**
- Blocker가 있으면 코드 수정 필요, Warning은 권장, Info는 참고
- 프로젝트의 기존 스타일과 아키텍처를 존중 (급진적 리팩터링 제안 자제)
