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

### Rules 작성 가이드라인 (중요)

Rules 문서는 매 요청마다 컨텍스트에 로드되어 토큰을 지속적으로 점유합니다. 최대한 간결하게 작성하세요:

- 불필요한 설명 제거 - 핵심만 남기기
- 코드 예시는 5-10줄 이내
- 중복 내용 없이 bullet point로 정리
- 장황한 문장 대신 키워드 중심으로 작성
