# Rules

As a supplementary plugin for Superpowers, rules are always auto-loaded and consume context window, so keep them minimal.
Concentrate domain knowledge in skills.

## Structure

```
rules/
├── common/
│   ├── coding-style.md  # DRY principles, immutability
│   └── turborepo.md     # Turborepo rules
├── typescript/
│   ├── coding-style.md  # no console.log, Zod, TS-specific rules
│   └── frontend.md      # colocation structure, TanStack Query
```

## Installation

### Option 1: Install Script (Recommended)

```bash
./install.sh typescript
```

### Option 2: Manual Installation

> **Important:** Copy entire directories — do NOT flatten with `/*`.

```bash
cp -r rules/common ~/.claude/rules/common
cp -r rules/typescript ~/.claude/rules/typescript
# turborepo-env.md is now inside common/
```
