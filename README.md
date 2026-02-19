# Skills & Workflow

Antigravity에 최적화된 Skills와 Workflow 구조입니다.
**세션별 워크플로우 분리**로 토큰 효율을 극대화합니다.

## 구조

```
skills-workflow/
├── skills/                      # 14개 스킬
│   ├── accessibility-review/    # 웹 접근성 (WCAG 2.1 AA)
│   ├── ai-code-audit/           # AI 생성 코드 감사
│   ├── ambiguity-checklist/     # 모호성 해소 체크리스트
│   ├── code-analysis/           # 코드 심층 분석 보고서
│   ├── code-quality-review/     # 코드 품질 + 효율성 + 리팩터링 리뷰
│   ├── debugging/               # 체계적 디버깅 (재현 → 근본 원인 분석)
│   ├── health-check/            # 배포 후 시스템 점검
│   ├── impact-analysis/         # 변경 영향 범위 분석
│   ├── logic-audit/             # 설계 논리 검증
│   ├── performance-review/      # 성능 심층 분석
│   ├── rollback-strategy/       # 롤백 전략 설계
│   ├── security-review/         # OWASP 기반 보안 점검
│   ├── test-strategy/           # 테스트 전략 + 품질 게이트
│   └── ui-ux-review/            # UI/UX 사용성 + 일관성 + 반응형
├── workflow/                    # 7개 워크플로우 (세션별 분리)
│   ├── plan.md                  # PLANNING 세션
│   ├── dev.md                   # EXECUTION 세션 (백엔드)
│   ├── ui-dev.md                # EXECUTION 세션 (프론트엔드)
│   ├── test.md                  # VERIFICATION — 테스트
│   ├── review.md                # VERIFICATION — 리뷰 (4+2 Gate)
│   ├── bugfix.md                # 버그 수정 사이클
│   └── analyzer.md              # 독립 코드 분석
└── README.md
```

## 설계 원칙

1. **세션 분리** — Phase별로 독립 세션 → 토큰 효율, 대형 코드베이스 대응
2. **프로젝트 내 `.docs/` 핸드오프** — 세션 간 공유 문서는 `{PROJECT_ROOT}/.docs/`에 저장
3. **경량 워크플로우** — 스킬 조합 순서만 정의, 세부 절차는 스킬에 위임

## 개발 흐름 (세션별)

### 백엔드 중심

```
/plan → /dev → /test → /review
```

### 프론트엔드 중심

```
/plan → /ui-dev → /test → /review (Gate 5,6 자동 활성화)
```

### 풀스택

```
/plan → /dev → /ui-dev → /test → /review (전체 Gate)
```

## 기타 워크플로우

| 워크플로우 | 용도 |
|-----------|------|
| `/bugfix` | 버그 수정 (DIAGNOSE→FIX→VERIFY) |
| `/analyzer` | 코드 분석 및 설명 |

## 스킬 직접 사용

| 스킬 | 트리거 예시 |
|------|------------|
| `accessibility-review` | "접근성 점검해줘" |
| `ai-code-audit` | "AI 코드 감사해줘" |
| `ambiguity-checklist` | "스펙 점검해줘" |
| `code-analysis` | "이 코드 분석해줘" |
| `code-quality-review` | "코드 리뷰해줘" |
| `debugging` | "이 에러 원인 찾아줘" |
| `health-check` | "배포 후 점검해줘" |
| `impact-analysis` | "영향 분석해줘" |
| `logic-audit` | "설계 리뷰해줘" |
| `performance-review` | "성능 점검해줘" |
| `rollback-strategy` | "롤백 전략 짜줘" |
| `security-review` | "보안 점검해줘" |
| `test-strategy` | "테스트 작성해줘" |
| `ui-ux-review` | "UI 리뷰해줘" |
