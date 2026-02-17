---
name: implementation-planning
description: |
  Implementation Plan 아티팩트를 생성하고 사용자 리뷰를 받는 스킬.
  새 기능, 리팩터링, 인프라 변경 등 모든 계획 수립 시 사용.
  트리거: "구현 계획", "implementation plan", "설계", "plan 작성"
---

# Implementation Planning

## Goal
실행 가능한 수준의 Implementation Plan 아티팩트를 생성하고,
사용자의 승인을 받은 후에만 구현을 시작하도록 합니다.

## Instructions

### Step 1: 작업 목표 요약

- 사용자 요청을 한 단락으로 요약
- 범위(Scope)를 명시: 무엇이 포함되고, 무엇이 이번 범위에서 제외되는지

### Step 2: 작업 분해 (Task Decomposition)

작업을 **독립적으로 실행 및 검증 가능한 단위(Chunk)**로 분해합니다.

**분해 원칙:**
- 각 Chunk는 빌드/테스트 가능한 상태를 유지
- Chunk 간 순서/의존 관계를 표시
- 하나의 Chunk = 하나의 코드 리뷰 단위

**분해 예시:**
```
Chunk 1: 데이터 모델/인터페이스 정의
Chunk 2: 핵심 비즈니스 로직 구현
Chunk 3: API 엔드포인트 연결
Chunk 4: 통합 및 설정
```

### Step 3: Implementation Plan 아티팩트 생성

아래 섹션을 포함하는 Plan 아티팩트를 생성합니다.

```markdown
# [작업 제목]

## Summary
작업 목적과 범위를 한 단락으로 요약.

## Proposed Changes
### [Component/Chunk 이름]
#### [MODIFY/NEW/DELETE] [파일명](file:///절대경로)
- 변경 내용 설명

## Data & API (해당 시)
- 데이터 모델 변경
- API 시그니처 변경

## Verification Steps
- 빌드/테스트 명령
- 수동 검증 체크리스트

## Rollback Plan (해당 시)
- 되돌리기 방법
```

### Step 4: 사용자 리뷰 루프

1. Plan을 사용자에게 보여주고 리뷰 요청
2. 피드백을 반영하여 Plan 업데이트
3. **사용자가 명시적으로 승인하기 전에는 코드를 변경하지 않음**
4. 승인 후 `incremental-execution` 스킬로 전환

## Constraints
- Plan 없이 대규모 변경을 시작하지 않음
- Plan에서 벗어나야 할 경우, 먼저 Plan을 갱신하고 이유 설명
- 한 번에 전부 구현하지 않고, Chunk 단위로 나눠서 구현 계획 수립
