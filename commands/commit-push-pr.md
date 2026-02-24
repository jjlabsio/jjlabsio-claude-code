---
description: Commit, push, and open a PR in one workflow
---

# Commit, Push, and PR

Commit changes using the `/commit` workflow, then push and open a PR.

## Usage

`/commit-push-pr [commit-args]`

## Workflow

### Step 1: Commit

Run the full `/commit` workflow (review, grouping, security checks).

$ARGUMENTS:
- If arguments provided (e.g., `feat "add login flow"`), pass as commit type and message to `/commit`
- If no arguments, run `/commit` in interactive mode

**If no commits were created (user cancelled, security issue, or no changes), STOP here.**

### Step 2: Push

1. Check current branch: `git branch --show-current`
2. **If on main/master**:
   - Derive branch name: `<type>/<short-description>` in kebab-case (e.g., `feat/add-user-auth`)
   - Create branch: `git checkout -b <branch-name>`
3. Push with upstream tracking: `git push -u origin <branch>`

**If push fails, report the error and STOP.**

### Step 3: Create PR

1. Detect base branch: `gh repo view --json defaultBranchRef -q '.defaultBranchRef.name'`
2. Check if PR already exists: `gh pr list --head <branch> --state open`
   - If PR exists, inform the user and STOP
3. Run `git log <base-branch>..HEAD --oneline` to gather commits
4. Generate title from commits (under 70 chars)
5. Create PR:

```bash
gh pr create --title "<title>" --body "$(cat <<'EOF'
## Summary
<bullet points from commits>

## Test plan
- [ ] Verify changes work as expected

Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

6. Return the PR URL to the user.

## Related

- `commands/commit.md` - Underlying commit workflow (review, grouping, security)
- `rules/git-workflow.md` - Commit and PR conventions
