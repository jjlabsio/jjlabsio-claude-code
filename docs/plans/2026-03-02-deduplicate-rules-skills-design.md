# Problem 2: Rules ↔ Skills 중복 제거 Design

## 배경

이 플러그인은 superpowers 보완 플러그인으로 전환되었다. superpowers가 워크플로우를 담당하므로,
이 플러그인의 rules는 최소한만 유지하고 도메인 지식은 skills에 집중한다.

Rules는 `paths:` 매칭으로 항상 자동 로딩되어 context window를 차지한다.
Skills는 on-demand로 로딩된다. 보완 플러그인으로서 항상 로딩할 가치가 있는 rules만 남긴다.

## 결정

### 삭제 (7개)

| 파일 | 이유 |
|------|------|
| `common/hooks.md` | CLAUDE.md "훅 동작" 섹션이 커버 |
| `common/git-workflow.md` | superpowers 영역 |
| `common/testing.md` | superpowers TDD skill 영역 |
| `common/patterns.md` | backend-patterns, frontend-patterns skills에 이미 존재 |
| `typescript/patterns.md` | skills와 완전 중복 (ApiResponse, useDebounce, Repository) |
| `typescript/hooks.md` | CLAUDE.md가 커버 |
| `typescript/testing.md` | e2e-testing skill이 커버 |
| `typescript/security.md` | common/security + skills가 커버 |

### 유지 (5개)

| 파일 | 이유 |
|------|------|
| `common/security.md` | 커밋 전 보안 체크리스트 — 항상 상기 가치 |
| `common/coding-style.md` | 네이밍 컨벤션, immutability, 코드 스멜 — 항상 적용 |
| `typescript/coding-style.md` | console.log 금지, Zod 사용 등 TS 고유 규칙 |
| `typescript/frontend.md` | colocation 구조, TanStack Query — 프로젝트 고유 |
| `turborepo-env.md` | turbo.json 환경변수 체크리스트 — 놓치면 캐시 문제 |

### 추가 작업

- `rules/README.md` 업데이트 (삭제 반영)
- `CLAUDE.md` 구성 섹션 업데이트
- `README.md` 스킬 개수 확인
- `install.sh` 업데이트 (삭제된 파일 참조 제거)
