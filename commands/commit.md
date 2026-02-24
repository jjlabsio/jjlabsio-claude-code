---
description: Create a well-formatted git commit following conventional commits
---

# Commit Command

`/commit [type] [message]`

## Workflow

1. **Analyze Changes**
   - Run `git status` and `git diff` to understand all changes
   - If staged changes exist: commit only staged changes
   - If no staged changes: group all changes into logical units

2. **Group & Stage** (when no staged changes)
   - Group by logical unit (feature + its tests, bugfix, refactor)
   - Exclude sensitive files (.env, credentials, secrets)
   - Stage each group separately for individual commits

3. **Pre-commit Safety Check** (inline, no agent)
   - Verify no secrets or credentials in staged files (.env, API keys, tokens)
   - Verify no sensitive files accidentally staged
   - If detected: STOP and warn user

4. **Commit**
   - Follow `rules/git-workflow.md` format: `<type>: <description>`
   - Auto-detect type from changes if not provided
   - Keep subject under 72 chars, imperative mood

## Grouping Example

Changes: `src/auth/login.ts`, `src/auth/login.test.ts`, `src/utils/format.ts`

Result:
1. `feat: add login authentication` (login.ts + login.test.ts)
2. `fix: correct date format output` (format.ts)
