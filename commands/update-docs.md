---
description: Update codemaps based on recent code changes. Auto-selects incremental or full scan strategy.
---

# Update Documentation

Analyze recent changes and update `docs/CODEMAPS/` accordingly.

## Execution

Delegate to **doc-updater** agent, which will:

1. Run `git diff` to identify changed files
2. Classify changes (Structural / Interface / Internal)
3. Select strategy:
   - **Full Scan** — new/deleted packages or first run → regenerate all codemaps
   - **Incremental** — interface changes only → update affected codemaps
   - **Skip** — internal changes only → report no updates needed

## When to Use

- After merging a feature branch
- Before a release
- When onboarding to a new codebase (first run → full scan)
- After adding/removing packages from the monorepo
