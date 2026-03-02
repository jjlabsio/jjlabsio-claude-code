# Rules

Superpowers 보완 플러그인으로서, rules는 항상 자동 로딩되어 context window를 차지하므로 최소한만 유지한다.
도메인 지식은 skills에 집중한다.

## Structure

```
rules/
├── common/
│   ├── coding-style.md  # 네이밍, immutability, 코드 스멜
│   ├── security.md      # 커밋 전 보안 체크리스트
│   └── turborepo.md     # Turborepo 규칙
├── typescript/
│   ├── coding-style.md  # console.log 금지, Zod, TS 고유 규칙
│   └── frontend.md      # colocation 구조, TanStack Query
├── python/              # Python specific
└── golang/              # Go specific
```

## Installation

### Option 1: Install Script (Recommended)

```bash
./install.sh typescript
./install.sh python
./install.sh golang

# Install multiple languages at once
./install.sh typescript python
```

### Option 2: Manual Installation

> **Important:** Copy entire directories — do NOT flatten with `/*`.

```bash
cp -r rules/common ~/.claude/rules/common
cp -r rules/typescript ~/.claude/rules/typescript
# turborepo-env.md is now inside common/
```
