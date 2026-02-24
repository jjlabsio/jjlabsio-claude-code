---
description: Commit, push, and open a PR in one workflow
---

# Commit, Push, and PR

`/commit-push-pr [commit-args]`

## Workflow

### Step 1: Commit

Run the full `/commit` workflow (grouping, safety check, commit).

$ARGUMENTS:
- If arguments provided (e.g., `feat "add login flow"`), pass as commit type and message
- If no arguments, run `/commit` in interactive mode

**If no commits were created, STOP here.**

### Step 2: Push

1. Check current branch: `git branch --show-current`
2. **If on main/master**: create branch `<type>/<short-description>` in kebab-case
3. Push with upstream tracking: `git push -u origin <branch>`

**If push fails, report the error and STOP.**

### Step 3: Create PR

1. Detect base branch: `gh repo view --json defaultBranchRef -q '.defaultBranchRef.name'`
2. Check if PR already exists: `gh pr list --head <branch> --state open`
   - If PR exists, inform user and STOP
3. Run `git log <base-branch>..HEAD --oneline` to gather commits
4. Generate title from commits (under 70 chars)
5. Create PR with summary bullet points and test plan
6. Return the PR URL to the user
