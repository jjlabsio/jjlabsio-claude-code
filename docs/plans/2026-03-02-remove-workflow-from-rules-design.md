# Remove Workflow Content from Rules

## Problem

Rules directory contains workflow content that belongs to superpowers (agent orchestration, model selection, extended thinking, TodoWrite usage, etc.), violating the plugin's design principle: "워크플로우는 superpowers가 담당하고, 이 플러그인은 도메인 지식 + 자동화 훅 + 패턴 학습을 제공한다."

## Changes

### 1. Delete `rules/common/agents.md`

Entire file is agent orchestration (superpowers territory). No domain knowledge to preserve.

### 2. Delete `rules/common/performance.md`

All 4 sections are workflow concerns:
- Model Selection Strategy (Haiku/Sonnet/Opus guidance)
- Context Window Management
- Extended Thinking + Plan Mode
- Build Troubleshooting (references build-error-resolver agent)

### 3. Trim `rules/common/git-workflow.md`

**Keep:** Commit Message Format, Pull Request Workflow
**Remove:** Feature Implementation Workflow section (references planner, tdd-guide, code-reviewer agents)

### 4. Trim `rules/common/hooks.md`

**Keep:** Hook Types overview
**Remove:** Auto-Accept Permissions section, TodoWrite Best Practices section

### 5. Remove agent references from other files

- `rules/common/testing.md` — remove "Use **tdd-guide** agent" line and Troubleshooting section
- `rules/common/security.md` — remove Security Response Protocol section (references security-reviewer agent)
- `rules/typescript/testing.md` — remove "Agent Support" section
- `rules/typescript/security.md` — remove "Agent Support" section

## Out of Scope

- Rules vs Skills duplication (separate task)
- Skills vs Skills duplication (separate task)
- rules/README.md update (update after changes)
