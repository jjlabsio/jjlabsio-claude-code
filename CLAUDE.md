# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repository Is

This is a Claude Code plugin (`jj@jjlabsio-claude-code`) that provides a curated collection of:
- **Agents** - Specialized sub-agents for planning, code review, TDD, security, etc.
- **Commands** - Slash commands like `/tdd`, `/code-review`, `/plan`
- **Skills** - Domain knowledge modules (React best practices, backend patterns, etc.)
- **Rules** - Auto-loaded guidelines for coding style, security, testing

## Repository Structure

```
.claude-plugin/
  marketplace.json    # Plugin marketplace registration
  plugin.json         # Plugin configuration (commands, skills, mcpServers)
agents/               # Specialized agent definitions (planner, tdd-guide, security-reviewer, etc.)
commands/             # Slash command implementations
hooks/                # Claude Code hooks configuration
rules/                # Auto-loaded rules (coding-style, security, testing, etc.)
skills/               # Domain knowledge modules
  vercel-react-best-practices/  # React/Next.js patterns and rules
  tdd-workflow/                  # TDD methodology
  backend-patterns/              # API and backend patterns
  ...
```

## Key Files

- `.claude-plugin/plugin.json` - Main plugin manifest defining what's exported
- `.claude-plugin/marketplace.json` - Marketplace registration for the plugin
- `hooks/hooks.json` - Claude Code hooks configuration
- `rules/*.md` - Auto-loaded rules that define coding standards
- `agents/*.md` - Agent prompts with frontmatter (name, description, tools, model)
- `commands/*.md` - Command prompts with frontmatter (description)

## Plugin Installation

Users install this plugin via:
```bash
/plugin marketplace add jjlabsio/jjlabsio-claude-code
/plugin install jj@jjlabsio-claude-code
```

Or by adding to `~/.claude/settings.json`:
```json
{
  "extraKnownMarketplaces": {
    "jjlabsio-claude-code": {
      "source": { "source": "github", "repo": "jjlabsio/jjlabsio-claude-code" }
    }
  },
  "enabledPlugins": { "jj@jjlabsio-claude-code": true }
}
```

## Agent Definitions

Agents are markdown files with YAML frontmatter:
```markdown
---
name: agent-name
description: What the agent does
tools: Read, Grep, Glob      # Available tools
model: opus                   # haiku, sonnet, or opus
---

[Agent prompt content]
```

Available agents: planner, architect, tdd-guide, code-reviewer, security-reviewer, refactor-cleaner

## Command Definitions

Commands are markdown files with description frontmatter:
```markdown
---
description: Short description shown in /help
---

[Command instructions]
```

## Development Conventions

When modifying this plugin:
- Keep files focused and under 400 lines
- Follow existing markdown formatting patterns
- Agent frontmatter must include: name, description, tools, model
- Command frontmatter must include: description
- Test changes by installing the plugin locally
- **Version updates**: Both `.claude-plugin/marketplace.json` and `.claude-plugin/plugin.json` must be updated together
- **Agent 추가 시**: `agents/` 디렉토리에 에이전트 파일을 추가하면 반드시 `.claude-plugin/plugin.json`의 `"agents"` 배열에도 경로를 등록해야 함

### 커밋 전 플러그인 호환성 체크 (필수)

플러그인 설정(`.claude-plugin/plugin.json`, `.mcp.json`, `hooks/hooks.json` 등)이 변경된 경우 커밋 전에 반드시:

1. **설치 호환성 확인**: 변경된 설정이 플러그인을 그대로 설치(`/plugin install`)해서 바로 사용 가능한지 검증
2. **추가 설정 필요 시 README 업데이트 강제**: 사용자가 별도 설정(환경변수, MCP 서버 설치, 외부 도구 등)을 해야 하는 경우 반드시 `README.md`에 설정 방법을 문서화
   - 예: 새로운 MCP 서버 추가 → README에 해당 서버 설치/설정 방법 기재
   - 예: 새로운 환경변수 필요 → README에 필요한 환경변수 목록과 설정법 기재

### Rules 작성 가이드라인 (중요)

Rules 문서는 매 요청마다 컨텍스트에 로드되어 토큰을 지속적으로 점유합니다. 최대한 간결하게 작성하세요:

- 불필요한 설명 제거 - 핵심만 남기기
- 코드 예시는 5-10줄 이내
- 중복 내용 없이 bullet point로 정리
- 장황한 문장 대신 키워드 중심으로 작성
