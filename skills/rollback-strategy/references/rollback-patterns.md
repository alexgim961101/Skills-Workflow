# Rollback Patterns Reference

## Pattern 1: Feature Flag Rollback
- 가장 안전한 롤백 방식
- 코드 변경 없이 기능 비활성화
- LaunchDarkly, Unleash, 자체 구현 등

## Pattern 2: Blue-Green Deployment
- 이전 버전 환경을 유지한 상태에서 전환
- 롤백 = 트래픽 스위칭
- 비용: 2배의 인프라

## Pattern 3: Database Rollback
- Forward-only migration 권장
- Backward-compatible 스키마 변경
  1. 새 컬럼 추가 (nullable)
  2. 코드에서 새 컬럼 사용
  3. 이전 컬럼 deprecated
  4. 충분한 시간 후 이전 컬럼 삭제

## Pattern 4: Canary Release
- 전체 트래픽의 5~10%만 새 버전으로
- 모니터링 후 점진적 확대
- 문제 발견 시 즉시 0%로
