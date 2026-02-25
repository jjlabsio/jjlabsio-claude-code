# Coding Style

## Design Principles

- **KISS**: Simplest solution that works. Easy to understand > clever code
- **DRY**: Extract common logic into reusable functions/components
- **YAGNI**: Don't build features before they're needed. Start simple, refactor when needed

## Naming Conventions

- Variables: descriptive names (`totalRevenue`, not `x`). Booleans: `is/has/can` prefix
- Functions: verb-noun pattern (`fetchMarketData`, `calculateSimilarity`, `isValidEmail`)
- Constants: UPPER_SNAKE_CASE for magic numbers (`MAX_RETRIES`, `DEBOUNCE_DELAY_MS`)
- Files: PascalCase for components (`Button.tsx`), camelCase for utils/hooks (`useAuth.ts`, `formatDate.ts`)

## Immutability (CRITICAL)

ALWAYS create new objects, NEVER mutate existing ones:

```
// Pseudocode
WRONG:  modify(original, field, value) → changes original in-place
CORRECT: update(original, field, value) → returns new copy with change
```

Rationale: Immutable data prevents hidden side effects, makes debugging easier, and enables safe concurrency.

## File Organization

MANY SMALL FILES > FEW LARGE FILES:
- High cohesion, low coupling
- 200-400 lines typical, 800 max
- Extract utilities from large modules
- Organize by feature/domain, not by type

## Error Handling

ALWAYS handle errors comprehensively:
- Handle errors explicitly at every level
- Provide user-friendly error messages in UI-facing code
- Log detailed error context on the server side
- Never silently swallow errors

## Input Validation

ALWAYS validate at system boundaries:
- Validate all user input before processing
- Use schema-based validation where available
- Fail fast with clear error messages
- Never trust external data (API responses, user input, file content)

## Async Patterns

- Use `Promise.all` for independent parallel operations
- Avoid sequential `await` when calls are independent

## Code Smells

- Deep nesting (>4 levels): use early returns/guard clauses
- Magic numbers: extract to named constants
- Long functions (>50 lines): split into smaller focused functions
- Comments explaining "what": rewrite code to be self-documenting. Comment only "why"

## Code Quality Checklist

Before marking work complete:
- [ ] Code is readable and well-named
- [ ] Functions are small (<50 lines)
- [ ] Files are focused (<800 lines)
- [ ] No deep nesting (>4 levels)
- [ ] Proper error handling
- [ ] No hardcoded values (use constants or config)
- [ ] No mutation (immutable patterns used)
