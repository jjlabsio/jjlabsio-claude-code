# Coding Style

## Design Principles

- **Business logic DRY**: 비즈니스 로직은 반드시 단일 출처(single source of truth) 유지. 코드의 표면적 유사성만으로 추상화하지 않는다

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

## Async Patterns

- Use `Promise.all` for independent parallel operations
- Avoid sequential `await` when calls are independent

## Code Smells

- Deep nesting (>4 levels): use early returns/guard clauses
- Magic numbers: extract to named constants
- Long functions (>50 lines): split into smaller focused functions

## Code Quality Checklist

Before marking work complete:
- [ ] Code is readable and well-named
- [ ] Functions are small (<50 lines)
- [ ] Files are focused (<800 lines)
- [ ] No deep nesting (>4 levels)
- [ ] No hardcoded values (use constants or config)
- [ ] No mutation (immutable patterns used)
