---
name: accessibility-review
description: |
  웹 접근성(a11y)을 WCAG 2.1 기준으로 점검하는 스킬.
  프론트엔드 코드 변경, 폼/모달/네비게이션 구현 시 사용.
  트리거: "접근성 점검", "a11y", "accessibility", "WCAG", "스크린 리더"
---

# Accessibility Review

## Goal
변경된 UI가 WCAG 2.1 AA 기준을 충족하는지 점검하고,
장애가 있는 사용자도 동등하게 사용할 수 있는지 검증합니다.

## Instructions

### Step 1: 변경 범위 파악

- 변경된 컴포넌트에서 접근성에 영향을 주는 요소 식별
- 인터랙티브 요소 (버튼, 링크, 폼, 모달, 드롭다운) 에 집중

### Step 2: 시맨틱 HTML

- [ ] **올바른 태그**: `<div onclick>` 대신 `<button>` 사용
- [ ] **헤딩 계층**: `<h1>` → `<h2>` → `<h3>` 순서 (건너뛰기 없음)
- [ ] **랜드마크**: `<nav>`, `<main>`, `<aside>`, `<footer>` 적절히 사용
- [ ] **리스트**: 목록 데이터에 `<ul>/<ol>/<li>` 사용
- [ ] **테이블**: 데이터 테이블에 `<th>`, `scope`, `caption` 사용

### Step 3: 키보드 내비게이션

- [ ] **포커스 가능**: 모든 인터랙티브 요소에 키보드로 접근 가능한가?
- [ ] **포커스 순서**: Tab 순서가 시각적 순서와 일치하는가?
- [ ] **포커스 표시**: 포커스된 요소가 시각적으로 구분되는가? (`outline` 제거 금지)
- [ ] **키보드 트랩 없음**: 모달/드롭다운에서 Tab으로 빠져나올 수 있는가?
- [ ] **단축키**: Enter(확인), Escape(닫기), 방향키(목록 탐색)가 작동하는가?

### Step 4: ARIA 및 스크린 리더

- [ ] **alt 텍스트**: 모든 `<img>`에 의미 있는 `alt` 속성 (장식 이미지는 `alt=""`)
- [ ] **aria-label**: 텍스트 없는 버튼(아이콘 버튼)에 `aria-label` 제공
- [ ] **aria-live**: 동적으로 변경되는 콘텐츠에 `aria-live` 설정 (토스트, 알림)
- [ ] **role**: 커스텀 컴포넌트에 적절한 `role` 지정 (dialog, tablist, menu 등)
- [ ] **aria-expanded/selected**: 토글, 아코디언, 탭의 상태를 전달하는가?
- [ ] **폼 라벨**: 모든 `<input>`에 연결된 `<label>` 또는 `aria-labelledby`

### Step 5: 시각적 접근성

- [ ] **색상 대비**: 텍스트/배경 대비 비율 4.5:1 이상 (AA 기준)
  - 큰 텍스트(18px 이상): 3:1 이상
- [ ] **색상 단독 전달 금지**: 색상만으로 정보를 전달하지 않는가? (아이콘/텍스트 병행)
- [ ] **텍스트 크기 조절**: 200%까지 확대해도 레이아웃이 깨지지 않는가?
- [ ] **모션 감소**: `prefers-reduced-motion` 미디어 쿼리 대응

### Step 6: 결과 보고

```
♿ Accessibility Review (WCAG 2.1 AA)

🔴 Critical (접근 불가):
  - [컴포넌트] 기준 위반: 설명 → 수정 방안

🟠 Warning (접근성 저하):
  - [컴포넌트] 기준: 설명 → 수정 방안

🟡 Best Practice:
  - [컴포넌트] 권장: 설명 → 개선 방안

판정: ✅ PASS / ❌ FAIL (🔴 Critical 1건 이상이면 FAIL)
```

## Constraints
- WCAG 2.1 AA를 기준으로 함 (AAA는 권장 사항으로만)
- 시맨틱 HTML 우선 — ARIA는 네이티브 HTML로 해결 불가 시에만
- 자동 도구(axe, lighthouse)와 수동 점검 병행 권장
