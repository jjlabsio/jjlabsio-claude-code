# Turborepo Environment Variables

## Trigger

Apply when adding or modifying environment variables in a turborepo project.

## Required Action

Always update `turbo.json` when changing environment variables.

### Classification

**`tasks.build.env`** - Affects build output:
- Variables with `NEXT_PUBLIC_*` prefix
- Variables bundled at build time

**`tasks.build.passThroughEnv`** - Required at runtime only:
- DB connections: `DATABASE_URL`
- Secrets: `*_SECRET`, `*_API_KEY`
- External service credentials

## Checklist

Before completing environment variable changes:
- [ ] Added to `env` or `passThroughEnv` in `turbo.json`
- [ ] Placed in the appropriate category

## Warning

If not registered in `turbo.json`:
- Vercel may use a cached previous build
- Environment variable values will not be reflected in the build
