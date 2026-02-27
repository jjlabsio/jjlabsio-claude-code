---
name: doc-full-scan
description: Full monorepo scan to generate complete codemaps from scratch. Use for first-time setup, structural changes (new/deleted packages), or pre-release documentation refresh.
---

# Full Codemap Scan

Generate complete `docs/CODEMAPS/` from scratch by scanning the entire monorepo.

## When to Activate

- No existing `docs/CODEMAPS/` directory (first run)
- Structural changes: new packages added, packages removed, workspace config changed
- Pre-release documentation refresh
- User explicitly requests full regeneration

## Workspace Discovery

Detect monorepo structure in order:

1. `pnpm-workspace.yaml` → parse `packages:` glob patterns
2. `package.json` → parse `workspaces` field
3. **Fallback**: scan `apps/`, `packages/`, `services/`, `libs/` directories

```bash
# List all workspace packages
ls -d apps/*/ packages/*/ services/*/ libs/*/ 2>/dev/null
```

## Full Scan Procedure

### Step 1: Create Root INDEX

```markdown
# Project Codemap Index

**Last Updated:** YYYY-MM-DD
**Workspace Type:** pnpm | npm | yarn
**Total Packages:** N

## Areas
| Area | Codemap | Packages | Description |
```

### Step 2: Classify Packages into Areas

| Area | Matching Patterns |
|------|------------------|
| **frontend** | `apps/web*`, `packages/ui*`, `packages/components*` |
| **backend** | `apps/api*`, `apps/server*`, `services/*`, `packages/server*` |
| **database** | `packages/db*`, `packages/prisma*`, `packages/drizzle*`, `**/migrations/` |
| **shared** | `packages/shared*`, `packages/utils*`, `packages/common*`, `packages/types*` |
| **infra** | `packages/config*`, `packages/eslint*`, `packages/tsconfig*` |
| **workers** | `apps/worker*`, `services/worker*`, `packages/queue*` |

Adapt classification to actual project structure. Merge areas with < 2 packages.

### Step 3: Generate Area Codemaps

For each area, create `docs/CODEMAPS/<area>.md` using canonical format:

1. **Scan entry points**: `index.ts`, `main.ts`, `app.ts`, route files
2. **Extract exports**: public API surface per module
3. **Map dependencies**: inter-package imports, external deps
4. **Identify patterns**: framework conventions (Next.js pages, Express routes, etc.)
5. **Document data flow**: how data moves through the area

### Step 4: Environment Variables

Scan all `.env*` files across packages. Add env vars to relevant area codemaps.

### Step 5: Cross-Reference Links

Add `Related Areas` section to each codemap linking to dependent areas.

## Output Structure

```
docs/CODEMAPS/
├── INDEX.md          # Overview, package list, area links
├── frontend.md       # Frontend apps and UI packages
├── backend.md        # API services and server packages
├── database.md       # Database schemas, migrations
├── shared.md         # Shared utilities and types
└── ...               # Additional areas as needed
```

## Codemap Format (Canonical)

```markdown
# [Area] Codemap

**Last Updated:** YYYY-MM-DD
**Entry Points:** list of main files

## Architecture
[ASCII diagram of component relationships]

## Key Modules
| Module | Purpose | Exports | Dependencies |

## Data Flow
[How data flows through this area]

## External Dependencies
- package-name - Purpose, Version

## Related Areas
Links to other codemaps
```

## Rules

- Each codemap must be under 500 lines
- Generate from actual code, never fabricate
- Skip empty or config-only packages
- Verify all referenced file paths exist

## Output Summary

```
Full Codemap Scan
──────────────────────────────
Created:  docs/CODEMAPS/INDEX.md (12 packages indexed)
Created:  docs/CODEMAPS/frontend.md (3 packages)
Created:  docs/CODEMAPS/backend.md (4 packages)
Created:  docs/CODEMAPS/database.md (2 packages)
Created:  docs/CODEMAPS/shared.md (3 packages)
Skipped:  packages/eslint-config (config-only)
──────────────────────────────
```
