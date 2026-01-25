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

2. **Security Review** (See: `rules/security.md`)
   - Use **security-reviewer** agent for code handling auth, user input, or secrets
   - Verify mandatory security checklist:
     - [ ] No hardcoded secrets (API keys, passwords, tokens)
     - [ ] All user inputs validated
     - [ ] SQL injection prevention (parameterized queries)
     - [ ] XSS prevention (sanitized HTML)
     - [ ] Error messages don't leak sensitive data

3. **Code Review** (See: `rules/agents.md`)
   - Use **code-reviewer** agent after writing/modifying code
   - Address CRITICAL and HIGH issues before committing
   - Fix MEDIUM issues when possible

4. **Stage Files**
   - If no files staged, prompt user to select files
   - Prefer staging specific files over `git add -A`
   - Never stage sensitive files (.env, credentials, secrets)

5. **Create Commit** (See: `rules/git-workflow.md`)
   - Format: `<type>: <description>`
   - Types: feat, fix, refactor, docs, test, chore, perf, ci
   - Keep subject line under 72 characters
   - Use imperative mood ("add feature" not "added feature")
   - Optional body explaining why, not what

## Examples

```bash
# Feature
git commit -m "feat: add user authentication flow"

# Bug fix
git commit -m "fix: resolve race condition in data fetching"

# Refactor
git commit -m "refactor: extract validation logic to separate module"
```

## Arguments

$ARGUMENTS:
- `type` - Commit type (feat, fix, refactor, docs, test, chore, perf, ci)
- `message` - Optional commit message (will be prompted if not provided)

## Interactive Mode

If no arguments provided:
1. Show `git status` output
2. Run **security-reviewer** if changes involve auth/input/secrets
3. Run **code-reviewer** for quality check
4. Ask which files to stage
5. Suggest commit type based on changes
6. Generate commit message draft
7. Confirm before committing

## Related Rules

- `rules/git-workflow.md` - Commit message format and types
- `rules/security.md` - Mandatory security checks before commit
- `rules/agents.md` - When to use security-reviewer and code-reviewer
