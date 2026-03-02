# CLAUDE.md

Superpowers 보완 플러그인. 워크플로우는 superpowers가 담당하고, 이 플러그인은 도메인 지식 + 자동화 훅 + 패턴 학습을 제공한다.

## 구성

```
commands/
  learn.md              # /learn — 세션 패턴 추출
skills/
  backend-patterns/     # API 아키텍처 규칙 (3계층 분리, 에러 핸들링, 캐싱, 로깅)
  database-migrations/  # DB 마이그레이션 안전 규칙 (expand-contract, safety checklist)
  api-dto-patterns/     # API Request DTO (Zod schema, 파일 구조, 네이밍)
hooks/
  hooks.json            # PostToolUse 3개 + Stop 1개
scripts/
  hooks/                # 훅 실행 스크립트
  lib/utils.js          # 공유 유틸리티
rules/                  # 최소 규칙 (코딩 스타일, 보안)
```

## 훅 동작

- **PostToolUse (Edit)**: Prettier 포맷팅 → TypeScript 타입체크 → console.log 경고
- **Stop**: 수정된 파일에서 console.log 최종 검사

## 개발 규칙

- 스킬은 도메인 지식만 제공 (워크플로우 스킬 금지 — superpowers 영역)
- 커맨드는 superpowers 워크플로우와 겹치지 않는 독립 기능만
- 훅은 PostToolUse/Stop만 사용 (PreToolUse, SessionStart 등은 superpowers 영역)

## README 싱크

플러그인 파일 변경 후 반드시 README.md와 일치하는지 확인하고, 불일치 시 README를 업데이트한다.

| 변경 대상 | README 확인 섹션 |
|-----------|-----------------|
| `skills/` 추가/삭제/이름변경 | `### 스킬` — 개수와 테이블 |
| `commands/` 추가/삭제 | `### 커맨드` — 개수와 테이블 |
| `hooks/hooks.json` 변경 | `### 훅` — 개수와 테이블 |
| `rules/` 추가/삭제 | `### Rules` — 디렉토리/파일 목록 |
| `.mcp.json` 변경 | `## MCP 서버` — 서버 목록 |
| `plugin.json` 변경 | 설명, 이름, 키워드 등 |
| `CLAUDE.md` 구성 변경 | `## 구성` — 트리 구조 |
