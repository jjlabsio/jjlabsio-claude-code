# CLAUDE.md

Superpowers 보완 플러그인. 워크플로우는 superpowers가 담당하고, 이 플러그인은 도메인 지식 + 자동화 훅 + 패턴 학습을 제공한다.

## 구성

```
commands/
  learn.md              # /learn — 세션 패턴 추출
skills/
  backend-patterns/     # API 아키텍처 규칙 (3계층 분리, 에러 핸들링, 캐싱, 로깅)
  database-migrations/  # DB 마이그레이션 안전 규칙 (expand-contract, safety checklist)
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
- `plugin.json`과 `hooks.json` 변경 시 README 업데이트 필수
