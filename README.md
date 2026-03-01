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

### 스킬 (8개 — 도메인 지식)

| 스킬 | 설명 |
|------|------|
| `frontend-patterns` | React/Next.js 컴포넌트, 상태 관리, 렌더링 패턴 |
| `backend-patterns` | Node.js 서버, 미들웨어, 에러 핸들링 패턴 |
| `api-design` | REST API 설계, 버전 관리, 응답 형식 |
| `database-migrations` | DB 스키마 변경, 마이그레이션 전략 |
| `postgres-patterns` | PostgreSQL 쿼리 최적화, 인덱싱, 성능 |
| `e2e-testing` | Playwright/Cypress E2E 테스트 패턴 |
| `deployment-patterns` | CI/CD, 배포 전략, 환경 설정 |
| `docker-patterns` | Dockerfile, docker-compose, 컨테이너 패턴 |

### 훅 (4개)

| 훅 | 타입 | 트리거 | 설명 |
|----|------|--------|------|
| post-edit-format | PostToolUse | Edit | Prettier 자동 포맷팅 |
| post-edit-typecheck | PostToolUse | Edit | TypeScript 타입 체크 |
| post-edit-console-warn | PostToolUse | Edit | console.log 경고 (라인 번호 포함) |
| check-console-log | Stop | * | 수정된 파일 console.log 최종 검사 |

### Rules

`install.sh`로 설치되는 코딩 규칙:

- `common/` — 코딩 스타일, 보안, 테스트, 성능, Git 워크플로우
- 언어별 규칙 (typescript 등)

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
