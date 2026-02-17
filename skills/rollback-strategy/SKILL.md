---
name: rollback-strategy
description: |
  설계 문서에 포함할 롤백 전략 설계 가이드.
  DB 마이그레이션, 배포 전략, 기능 릴리즈 설계 시 사용.
  트리거: "롤백 전략", "마이그레이션 안전성", "배포 계획", "breaking change"
---

# Rollback Strategy Design

## Goal
모든 설계에 안전한 롤백 플랜을 포함시킵니다.

## Instructions

### 롤백 설계 체크리스트
1. DB 마이그레이션이 backward-compatible 한가?
2. Feature flag로 기능 비활성화 가능한가?
3. Blue-Green / Canary 배포 전략 적용 가능한가?
4. 데이터 복구 시나리오 (백업 시점, 복원 절차)
5. 의존 서비스 롤백 순서

### 위험도별 롤백 방식
- **Low**: Feature flag OFF
- **Medium**: 이전 버전 재배포 + DB forward-compatible
- **High**: 전체 롤백 + 데이터 복원 + 의존 서비스 동기 롤백

## Constraints
- 롤백 불가능한 설계는 반드시 사전 경고
- 데이터 손실 가능성이 있는 마이그레이션은 반드시 백업 전략 포함

## References
추가 롤백 패턴은 `references/rollback-patterns.md` 참조
