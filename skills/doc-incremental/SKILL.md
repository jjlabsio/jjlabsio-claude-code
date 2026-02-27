---
name: doc-incremental
description: Incrementally update codemaps for changed packages only. Use when existing codemaps exist and changes are limited to interface files within known packages.
---

# Incremental Codemap Update

Update only the codemaps affected by recent code changes, preserving unchanged sections.

## When to Activate

- Existing `docs/CODEMAPS/` directory with prior codemaps
- Changes limited to **Interface** files within existing packages
- No new packages added or removed

## Scope Determination

1. Run `git diff --name-only HEAD~1` (or compare against base branch)
2. Extract unique package paths from changed files
3. Filter to packages that have existing codemaps

```bash
# Example: extract affected packages
git diff --name-only HEAD~1 | sed 's|/[^/]*$||' | sort -u
```

## Change Classification → Doc Section Mapping

| Changed File Pattern | Codemap Section to Update |
|---------------------|--------------------------|
| `*/index.ts`, `*/exports.ts` | **Key Modules** (exports column) |
| `*/route.ts`, `*/api/**` | **Architecture**, **Data Flow** |
| `package.json` (deps changed) | **External Dependencies** |
| `*.env*`, env config | **Environment Variables** (if section exists) |
| `Dockerfile`, `docker-compose.*` | **Infrastructure** (if section exists) |
| `*/types.ts`, `*/schema.*` | **Key Modules** (purpose/exports) |

## Per-Package Update Procedure

For each affected package:

1. **Read** current codemap (`docs/CODEMAPS/<area>.md`)
2. **Scan** changed interface files — extract exports, routes, dependencies
3. **Update** only the sections mapped by classification table above
4. **Preserve** unchanged sections verbatim
5. **Update** `Last Updated` timestamp

### Rules

- Never rewrite sections unrelated to the change
- Keep codemap under 500 lines
- If a change affects cross-package references, update `Related Areas` links in both codemaps
- Root `INDEX.md`: only update if package list or high-level architecture changed

## Output Summary

After completing updates, report:

```
Incremental Codemap Update
──────────────────────────────
Updated:  docs/CODEMAPS/frontend.md (Key Modules, External Dependencies)
Updated:  docs/CODEMAPS/backend.md (Data Flow)
Skipped:  docs/CODEMAPS/database.md (no interface changes)
Skipped:  docs/CODEMAPS/INDEX.md (structure unchanged)
──────────────────────────────
```
