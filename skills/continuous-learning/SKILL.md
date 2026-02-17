---
name: learn
description: Extract reusable patterns from Claude Code sessions and save them as learned skills. Works both manually (invoke mid-session) and automatically (Stop hook at session end).
---

# Learn - Extract Reusable Patterns

세션 중 비자명한 문제를 해결했을 때, 재사용 가능한 패턴을 추출하여 `~/.claude/skills/learned/`에 저장합니다.

## Usage

- **수동**: 세션 중 `/learn` 호출
- **자동**: Stop hook으로 세션 종료 시 자동 실행 (10+ 메시지 세션만)

## What to Extract

- **Error Resolution** - 에러 원인과 해결 과정
- **Debugging Techniques** - 비자명한 디버깅 기법, 도구 조합
- **Workarounds** - 라이브러리/API 우회법, 버전별 이슈
- **Project-Specific Patterns** - 코드베이스 컨벤션, 아키텍처 결정

## What NOT to Extract

- 단순 오타, 문법 에러
- 일회성 이슈 (API 장애 등)
- 외부 서비스 문제

## Process

1. 세션에서 추출 가능한 패턴 리뷰
2. 가장 재사용 가치 높은 패턴 식별
3. 스킬 파일 초안 작성
4. **사용자 확인 후** `~/.claude/skills/learned/`에 저장

## Output Format

```markdown
# [Descriptive Pattern Name]

**Extracted:** [Date]
**Context:** [Brief description of when this applies]

## Problem
[What problem this solves - be specific]

## Solution
[The pattern/technique/workaround]

## Example
[Code example if applicable]

## When to Use
[Trigger conditions - what should activate this skill]
```

## Auto-extraction Hook Setup

`~/.claude/settings.json`에 추가:

```json
{
  "hooks": {
    "Stop": [{
      "matcher": "*",
      "hooks": [{
        "type": "command",
        "command": "~/.claude/skills/continuous-learning/evaluate-session.sh"
      }]
    }]
  }
}
```

## Configuration

`config.json`으로 자동 추출 설정 커스터마이즈 가능 (min_session_length, patterns_to_detect 등).
