---
description: "UI Dev — 컴포넌트 기반 점진 구현, 브라우저 검증, 디자인 시스템 준수 (별도 세션에서 실행)"
---

# UI Dev (EXECUTION Session — Frontend)

컴포넌트 기반으로 UI를 점진적으로 구현합니다.
**`.docs/implementation_plan.md`를 읽고 시작합니다.**

> `task_boundary(Mode=EXECUTION)` 으로 시작

## Pre-Flight

1. **Plan 로딩**: `.docs/implementation_plan.md`를 읽습니다.
2. **Dev Server Detection**: 프로젝트에서 개발 서버를 탐지합니다.

| 프레임워크 | 탐지 | 개발 서버 |
|-----------|------|----------|
| Next.js | `next.config.*` | `npm run dev` |
| Vite | `vite.config.*` | `npm run dev` |
| CRA | `react-scripts` in package.json | `npm start` |
| Vue CLI | `vue.config.*` | `npm run serve` |
| Nuxt | `nuxt.config.*` | `npm run dev` |

3. **디자인 시스템 확인**: 프로젝트에 디자인 토큰/컴포넌트 라이브러리가 있는지 확인.
   - CSS Variables, Theme 파일, Storybook 등

## Component Chunk Loop (핵심)

### 1. 컴포넌트 기반 Chunk 분할

**분할 원칙 (백엔드와 다름):**
- 원자 → 조합 순서: **기본 컴포넌트 → 복합 컴포넌트 → 페이지**
- 각 Chunk는 브라우저에서 시각적으로 확인 가능한 단위
- 스타일과 로직은 같은 Chunk에 포함
- 반응형은 컴포넌트 구현 시 함께 처리 (나중에 별도로 하지 않음)

`.docs/task.md`에 체크리스트로 기록.

### 2. Chunk 구현 → 시각적 검증 → 리뷰 반복

```
각 Chunk마다:
  .docs/task.md [/] 마킹 → 코딩
  ├─ 1. 컴포넌트 구현 (마크업 + 스타일 + 로직)
  ├─ 2. 디자인 시스템 토큰 사용 확인
  ├─ 3. 브라우저에서 시각적 확인
  │     ├─ Desktop 뷰
  │     ├─ Mobile 뷰 (반응형)
  │     └─ 인터랙션 동작 (hover, click, 폼)
  ├─ 4. 빌드 검증 ({VERIFY_CMD})
  │     ├─ ❌ → 수정 후 재실행
  │     └─ ✅ ↓
  └─ 5. notify_user 리뷰 요청 (스크린샷 포함)
       ├─ "승인" → [x] 마킹 → 다음 Chunk
       ├─ "수정 요청" → 반영 후 재제출
       └─ "중단" → 현재까지 저장 후 종료
```

**백엔드 `/dev`와의 차이:**
- 빌드 검증 전에 **브라우저 시각적 확인** 단계 추가
- 리뷰 요청 시 **스크린샷** 포함 (generate_image 또는 browser 도구 활용)
- **반응형 체크**가 각 Chunk에 포함

### 3. 스펙 변경 프로토콜

`/dev`와 동일한 Minor/Medium/Major 프로토콜 적용.
디자인 변경의 경우:

| 규모 | 예시 | 대응 |
|------|------|------|
| **Minor** | 색상 변경, 패딩 조정 | 현재 Chunk에서 바로 반영 |
| **Medium** | 레이아웃 변경, 새 컴포넌트 추가 | Plan 갱신 → Chunk 재분할 |
| **Major** | 전체 디자인 리뉴얼 | 새 `/plan` 세션 |

## Hand-off

```
✅ UI 구현 완료

구현된 Chunk: N/N (컴포넌트 N개, 페이지 M개)
반응형: Desktop ✅ / Mobile ✅
디자인 시스템 준수: ✅

다음 단계: 새 세션에서 /test → /review 로 품질 검증을 시작하세요.
```
