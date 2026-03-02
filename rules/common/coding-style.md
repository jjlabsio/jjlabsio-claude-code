# Coding Style

## Design Principles

- **Business logic DRY**: 비즈니스 로직은 반드시 단일 출처(single source of truth) 유지. 코드의 표면적 유사성만으로 추상화하지 않는다

## Immutability (CRITICAL)

ALWAYS create new objects, NEVER mutate existing ones:

```
// Pseudocode
WRONG:  modify(original, field, value) → changes original in-place
CORRECT: update(original, field, value) → returns new copy with change
```

Rationale: Immutable data prevents hidden side effects, makes debugging easier, and enables safe concurrency.
