# jj — Superpowers 보완 플러그인

[superpowers](https://github.com/anthropics/superpowers) 플러그인과 함께 사용하도록 설계된 보완 플러그인.

superpowers가 **워크플로우**(brainstorming → planning → executing → review)를 담당하고, 이 플러그인은 **도메인 지식**, **자동화 훅**, **패턴 학습**을 제공한다.

## 설치

```bash
# 플러그인 설치
/plugin install jj@jjlabsio-claude-code

# Rules 설치 (선택)
./install.sh typescript
```

## 구성 요소

### 커맨드 (1개)

| 커맨드 | 설명 |
|--------|------|
| `/learn` | 세션에서 반복 패턴을 추출하여 스킬로 저장 |

### 스킬 (3개 — 도메인 지식)

| 스킬 | 설명 |
|------|------|
| `backend-patterns` | Backend architecture rules — error handling, caching, logging, data access |
| `database-migrations` | Database migration safety rules, zero-downtime procedures |
| `api-dto-patterns` | API Request DTO — Zod schema, file structure, naming (Next.js / NestJS) |

### 훅 (4개)

| 훅 | 타입 | 트리거 | 설명 |
|----|------|--------|------|
| post-edit-format | PostToolUse | Edit | Prettier 자동 포맷팅 |
| post-edit-typecheck | PostToolUse | Edit | TypeScript 타입 체크 |
| post-edit-console-warn | PostToolUse | Edit | console.log 경고 (라인 번호 포함) |
| check-console-log | Stop | * | 수정된 파일 console.log 최종 검사 |

### Rules (최소 유지)

`install.sh`로 설치되는 항상-적용 규칙 (도메인 지식은 skills에):

- `common/coding-style.md` — DRY 원칙, 설계 원칙
- `common/turborepo.md` — Turborepo 환경변수 체크리스트
- `typescript/coding-style.md` — TS 코딩 스타일 (`*.ts`, `*.tsx`, `*.js`)
- `typescript/frontend.md` — 프론트엔드 구조 (`*.tsx`, `*.jsx`)

## Superpowers와의 공존 원칙

1. **워크플로우 스킬 없음** — brainstorming, planning, TDD 등은 superpowers 영역
2. **에이전트 없음** — code-reviewer 등은 superpowers가 제공
3. **PostToolUse/Stop 훅만** — PreToolUse, SessionStart 등은 superpowers 영역
4. **1:1 원칙** — 하나의 행동에 두 가지 스킬/커맨드가 존재하지 않음

## MCP 서버

`.mcp.json`에 설정된 MCP 서버:

- **context7** — 라이브러리 문서 검색
- **firecrawl** — 웹 스크래핑/검색
- **vercel** — Vercel 배포 관리

## 라이선스

MIT
