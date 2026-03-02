# Remove Workflow Content from Rules — Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Remove all superpowers workflow content (agent orchestration, model selection, extended thinking, TodoWrite) from rules/, leaving only domain knowledge and coding standards.

**Architecture:** Pure file deletion and editing — no new files created. Each task is one file operation followed by a verification grep and a commit.

**Tech Stack:** Markdown files, git

---

### Task 1: Delete `rules/common/agents.md`

**Files:**
- Delete: `rules/common/agents.md`

**Step 1: Delete the file**

```bash
rm rules/common/agents.md
```

**Step 2: Verify no other files reference it**

```bash
grep -r "agents.md" rules/
```

Expected: No matches (or only README.md which we update in Task 8).

**Step 3: Commit**

```bash
git add rules/common/agents.md
git commit -m "refactor: remove agents.md rule (superpowers territory)"
```

---

### Task 2: Delete `rules/common/performance.md`

**Files:**
- Delete: `rules/common/performance.md`

**Step 1: Delete the file**

```bash
rm rules/common/performance.md
```

**Step 2: Verify no other files reference it**

```bash
grep -r "performance.md" rules/
```

Expected: No matches (or only README.md).

**Step 3: Commit**

```bash
git add rules/common/performance.md
git commit -m "refactor: remove performance.md rule (superpowers territory)"
```

---

### Task 3: Trim `rules/common/git-workflow.md`

**Files:**
- Modify: `rules/common/git-workflow.md`

**Step 1: Remove the "Feature Implementation Workflow" section**

Keep lines 1-23 (Commit Message Format + Pull Request Workflow). Delete lines 24-46 (Feature Implementation Workflow).

After edit, file should contain:

```markdown
# Git Workflow

## Commit Message Format

\```
<type>: <description>

<optional body>
\```

Types: feat, fix, refactor, docs, test, chore, perf, ci

Note: Attribution disabled globally via ~/.claude/settings.json.

## Pull Request Workflow

When creating PRs:
1. Analyze full commit history (not just latest commit)
2. Use `git diff [base-branch]...HEAD` to see all changes
3. Draft comprehensive PR summary
4. Include test plan with TODOs
5. Push with `-u` flag if new branch
```

**Step 2: Verify no agent references remain**

```bash
grep -i "agent\|planner\|tdd-guide\|code-reviewer" rules/common/git-workflow.md
```

Expected: No matches.

**Step 3: Commit**

```bash
git add rules/common/git-workflow.md
git commit -m "refactor: remove workflow section from git-workflow rule"
```

---

### Task 4: Trim `rules/common/hooks.md`

**Files:**
- Modify: `rules/common/hooks.md`

**Step 1: Remove "Auto-Accept Permissions" and "TodoWrite Best Practices" sections**

Keep lines 1-7 (Hook Types). Delete lines 9-31 (Auto-Accept Permissions + TodoWrite).

After edit, file should contain:

```markdown
# Hooks System

## Hook Types

- **PreToolUse**: Before tool execution (validation, parameter modification)
- **PostToolUse**: After tool execution (auto-format, checks)
- **Stop**: When session ends (final verification)
```

**Step 2: Verify clean**

```bash
grep -i "TodoWrite\|auto-accept\|allowedTools\|permissions" rules/common/hooks.md
```

Expected: No matches.

**Step 3: Commit**

```bash
git add rules/common/hooks.md
git commit -m "refactor: remove workflow sections from hooks rule"
```

---

### Task 5: Trim `rules/common/testing.md`

**Files:**
- Modify: `rules/common/testing.md`

**Step 1: Remove "Troubleshooting Test Failures" and "Agent Support" sections**

Keep lines 1-18 (Coverage + TDD). Delete lines 20-30 (Troubleshooting + Agent Support).

After edit, file should contain:

```markdown
# Testing Requirements

## Minimum Test Coverage: 80%

Test Types (ALL required):
1. **Unit Tests** - Individual functions, utilities, components
2. **Integration Tests** - API endpoints, database operations
3. **E2E Tests** - Critical user flows (framework chosen per language)

## Test-Driven Development

MANDATORY workflow:
1. Write test first (RED)
2. Run test - it should FAIL
3. Write minimal implementation (GREEN)
4. Run test - it should PASS
5. Refactor (IMPROVE)
6. Verify coverage (80%+)
```

**Step 2: Verify no agent references remain**

```bash
grep -i "agent\|tdd-guide" rules/common/testing.md
```

Expected: No matches.

**Step 3: Commit**

```bash
git add rules/common/testing.md
git commit -m "refactor: remove agent references from testing rule"
```

---

### Task 6: Trim `rules/common/security.md`

**Files:**
- Modify: `rules/common/security.md`

**Step 1: Remove "Security Response Protocol" section**

Keep lines 1-21 (Mandatory Security Checks + Secret Management). Delete lines 22-30 (Security Response Protocol).

After edit, file should contain:

```markdown
# Security Guidelines

## Mandatory Security Checks

Before ANY commit:
- [ ] No hardcoded secrets (API keys, passwords, tokens)
- [ ] All user inputs validated
- [ ] SQL injection prevention (parameterized queries)
- [ ] XSS prevention (sanitized HTML)
- [ ] CSRF protection enabled
- [ ] Authentication/authorization verified
- [ ] Rate limiting on all endpoints
- [ ] Error messages don't leak sensitive data

## Secret Management

- NEVER hardcode secrets in source code
- ALWAYS use environment variables or a secret manager
- Validate that required secrets are present at startup
- Rotate any secrets that may have been exposed
```

**Step 2: Verify no agent references remain**

```bash
grep -i "agent\|security-reviewer" rules/common/security.md
```

Expected: No matches.

**Step 3: Commit**

```bash
git add rules/common/security.md
git commit -m "refactor: remove agent references from security rule"
```

---

### Task 7: Trim `rules/typescript/testing.md` and `rules/typescript/security.md`

**Files:**
- Modify: `rules/typescript/testing.md`
- Modify: `rules/typescript/security.md`

**Step 1: Remove "Agent Support" section from typescript/testing.md**

Delete lines 16-18. After edit:

```markdown
---
paths:
  - "**/*.ts"
  - "**/*.tsx"
  - "**/*.js"
  - "**/*.jsx"
---
# TypeScript/JavaScript Testing

> This file extends [common/testing.md](../common/testing.md) with TypeScript/JavaScript specific content.

## E2E Testing

Use **Playwright** as the E2E testing framework for critical user flows.
```

**Step 2: Remove "Agent Support" section from typescript/security.md**

Delete lines 26-28. After edit:

```markdown
---
paths:
  - "**/*.ts"
  - "**/*.tsx"
  - "**/*.js"
  - "**/*.jsx"
---
# TypeScript/JavaScript Security

> This file extends [common/security.md](../common/security.md) with TypeScript/JavaScript specific content.

## Secret Management

\```typescript
// NEVER: Hardcoded secrets
const apiKey = "sk-proj-xxxxx"

// ALWAYS: Environment variables
const apiKey = process.env.OPENAI_API_KEY

if (!apiKey) {
  throw new Error('OPENAI_API_KEY not configured')
}
\```
```

**Step 3: Verify no agent references in either file**

```bash
grep -i "agent\|e2e-runner\|security-reviewer" rules/typescript/testing.md rules/typescript/security.md
```

Expected: No matches.

**Step 4: Commit**

```bash
git add rules/typescript/testing.md rules/typescript/security.md
git commit -m "refactor: remove agent references from typescript rules"
```

---

### Task 8: Update `rules/README.md`

**Files:**
- Modify: `rules/README.md`

**Step 1: Remove `agents.md` and `performance.md` from the directory tree listing**

In the tree structure, remove the lines:
```
│   ├── performance.md
│   ├── agents.md
```

**Step 2: Final verification — grep entire rules/ for agent references**

```bash
grep -ri "agent\|planner\|tdd-guide\|code-reviewer\|security-reviewer\|build-error-resolver\|e2e-runner\|refactor-cleaner\|doc-updater\|TodoWrite" rules/
```

Expected: No matches.

**Step 3: Commit**

```bash
git add rules/README.md
git commit -m "docs: update rules README to reflect removed files"
```
