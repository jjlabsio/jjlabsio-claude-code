---
description: Create a well-formatted git commit following conventional commits
---

# Commit Command

Create a git commit following the project's git-workflow and security rules.

## Usage

`/commit [type] [message]`

## Workflow

1. **Check Status**
   - Run `git status` to see all changes
   - Run `git diff --staged` to see staged changes
   - Run `git diff` to see unstaged changes

2. **Determine Commit Strategy**
   - **If staged changes exist**: Commit only staged changes (ignore unstaged)
   - **If no staged changes**: Stage and commit all changes with grouping logic

3. **Security Review** (See: `code-review` skill for full checklist)
   - Use **security-reviewer** agent for code handling auth, user input, or secrets
   - Verify no hardcoded secrets, proper input validation, and injection prevention

4. **Code Review** (See: `rules/agents.md`)
   - Use **code-reviewer** agent after writing/modifying code
   - Address CRITICAL and HIGH issues before committing
   - Fix MEDIUM issues when possible

5. **Stage Files** (only when no staged changes)
   - Analyze changes and group by logical units (feature, bugfix, refactor, etc.)
   - Create separate commits for each meaningful unit
   - Automatically exclude sensitive files (.env, credentials, secrets)

6. **Create Commit** (See: `rules/git-workflow.md`)
   - Format: `<type>: <description>`
   - Types: feat, fix, refactor, docs, test, chore, perf, ci
   - Keep subject line under 72 characters
   - Use imperative mood ("add feature" not "added feature")
   - Optional body explaining why, not what

## Examples

```bash
# Single commits
git commit -m "feat: add user authentication flow"
git commit -m "fix: resolve race condition in data fetching"
git commit -m "refactor: extract validation logic to separate module"
```

## Grouping Example

If changes include:
- `src/auth/login.ts` (new feature)
- `src/auth/login.test.ts` (test for above)
- `src/utils/format.ts` (bug fix)
- `README.md` (docs update)

Create 3 separate commits:
1. `feat: add login authentication` (login.ts + login.test.ts)
2. `fix: correct date format output` (format.ts)
3. `docs: update README` (README.md)

## Arguments

$ARGUMENTS:
- `type` - Commit type (feat, fix, refactor, docs, test, chore, perf, ci)
- `message` - Optional commit message (will be prompted if not provided)

## Interactive Mode

If no arguments provided:
1. Show `git status` and `git diff --staged` to analyze changes
2. **If staged changes exist** (regardless of unstaged):
   - Commit only the staged changes
   - Run **security-reviewer** if staged changes involve auth/input/secrets
   - Run **code-reviewer** for quality check on staged changes
   - Auto-detect commit type and generate message
   - Create single commit for staged changes
3. **If no staged changes** (only unstaged):
   - Run **security-reviewer** if changes involve auth/input/secrets
   - Run **code-reviewer** for quality check
   - Group changes into logical units:
     - By feature/functionality
     - By file relationship (e.g., component + test + styles)
     - By change type (feat vs fix vs refactor)
   - For each logical unit:
     - Stage related files
     - Auto-detect commit type
     - Auto-generate commit message
     - Create commit
   - Repeat until all changes are committed

## Related Rules

- `rules/git-workflow.md` - Commit message format and types
- `rules/security.md` - Mandatory security checks before commit
- `rules/agents.md` - When to use security-reviewer and code-reviewer
