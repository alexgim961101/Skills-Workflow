---
name: walkthrough-generation
description: |
  작업 결과를 Walkthrough 아티팩트로 정리하고, PR 설명으로 사용할 수 있도록 작성하는 스킬.
  구현 완료 후, PR 제출 전, 코드 리뷰 준비 시 사용.
  트리거: "워크스루", "결과 요약", "PR 설명", "walkthrough"
---

# Walkthrough Generation

## Goal
완료된 작업을 체계적으로 정리하여, 팀원이 빠르게 이해할 수 있는 Walkthrough 문서를 생성합니다.
PR 설명에 그대로 사용할 수 있는 형식으로 작성합니다.

## Instructions

### Step 1: Plan 대비 구현 비교

관련 Implementation Plan이 있다면:
- Plan에서 약속한 변경 vs 실제 변경 비교
- 누락, 추가, 변경된 점 목록화
- 차이가 있으면 사유 설명

### Step 2: 변경 내용 정리

아래 구조로 Walkthrough 아티팩트를 작성합니다.

```markdown
# Walkthrough: [작업 제목]

## 변경 요약
한 단락으로 무엇을 왜 변경했는지 요약.

## 주요 변경 사항
### [Component/Module 이름]
- **[파일명]**: 변경 내용 설명
- render_diffs(file:///절대경로)  ← 가능한 경우

## 테스트 결과
- 실행한 테스트 명령과 결과
- 새로 추가한 테스트 목록

## 알려진 제한 사항 / TODO
- 이번 범위에서 제외된 항목
- 후속 작업이 필요한 부분

## 후속 모니터링 (해당 시)
- 배포 후 관찰해야 할 지표
```

### Step 3: PR 설명 생성 (선택)

사용자가 요청하면 PR 설명 형식으로도 생성합니다.

```
feat: [제목]

## What
- 변경 내용 요약

## Why
- 변경 이유/배경

## How
- 구현 방식 요약

## Testing
- 테스트 방법 및 결과

## Checklist
- [ ] 빌드 통과
- [ ] 테스트 통과
- [ ] 문서 업데이트
```

## Constraints
- Walkthrough는 코드를 읽지 않은 팀원도 이해할 수 있는 수준으로 작성
- 스크린샷, 다이어그램이 있으면 포함
- 추측이 아닌 실제 결과만 기술
