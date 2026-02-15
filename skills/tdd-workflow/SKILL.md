---
name: tdd
description: Enforce test-driven development workflow. Scaffold interfaces, generate tests FIRST, then implement minimal code to pass. Ensure 80%+ coverage.
---

# Test-Driven Development Workflow

This skill enforces TDD methodology and invokes the **tdd-guide** agent.

## When to Use

- Implementing new features or components
- Adding new functions/APIs
- Fixing bugs (write test that reproduces bug first)
- Refactoring existing code
- Building critical business logic

## Core TDD Cycle

```
RED → GREEN → REFACTOR → REPEAT

RED:      Write a failing test
GREEN:    Write minimal code to pass
REFACTOR: Improve code, keep tests passing
```

## Workflow Steps

### Step 1: Scaffold Interfaces
Define types/interfaces for inputs and outputs first.

### Step 2: Write Failing Test (RED)
Write tests that FAIL because implementation doesn't exist yet.

### Step 3: Run Tests - Verify FAIL
Run tests and confirm they fail for the right reason.

### Step 4: Implement Minimal Code (GREEN)
Write just enough code to make tests pass.

### Step 5: Run Tests - Verify PASS
Confirm all tests pass.

### Step 6: Refactor (IMPROVE)
Improve code while keeping tests green:
- Remove duplication
- Improve naming
- Optimize performance

### Step 7: Verify Coverage
```bash
npm run test:coverage
# Verify 80%+ coverage
```

## Coverage Requirements

- **80% minimum** for all code
- **100% required** for:
  - Financial calculations
  - Authentication logic
  - Security-critical code
  - Core business logic

## Test Types

### Unit Tests (Jest/Vitest)
```typescript
import { render, screen, fireEvent } from '@testing-library/react'

describe('Button Component', () => {
  it('renders with correct text', () => {
    render(<Button>Click me</Button>)
    expect(screen.getByText('Click me')).toBeInTheDocument()
  })

  it('calls onClick when clicked', () => {
    const handleClick = jest.fn()
    render(<Button onClick={handleClick}>Click</Button>)
    fireEvent.click(screen.getByRole('button'))
    expect(handleClick).toHaveBeenCalledTimes(1)
  })
})
```

### API Integration Tests
```typescript
import { NextRequest } from 'next/server'
import { GET } from './route'

describe('GET /api/markets', () => {
  it('returns markets successfully', async () => {
    const request = new NextRequest('http://localhost/api/markets')
    const response = await GET(request)
    expect(response.status).toBe(200)
  })

  it('validates query parameters', async () => {
    const request = new NextRequest('http://localhost/api/markets?limit=invalid')
    const response = await GET(request)
    expect(response.status).toBe(400)
  })
})
```

### E2E Tests (Playwright)
```typescript
import { test, expect } from '@playwright/test'

test('user can search and filter markets', async ({ page }) => {
  await page.goto('/')
  await page.fill('input[placeholder="Search markets"]', 'election')
  await page.waitForTimeout(600)

  const results = page.locator('[data-testid="market-card"]')
  await expect(results).toHaveCount(5, { timeout: 5000 })
})
```

## Mocking External Dependencies

### Supabase
```typescript
jest.mock('@/lib/supabase', () => ({
  supabase: {
    from: jest.fn(() => ({
      select: jest.fn(() => ({
        eq: jest.fn(() => Promise.resolve({ data: [{ id: 1 }], error: null }))
      }))
    }))
  }
}))
```

### Redis
```typescript
jest.mock('@/lib/redis', () => ({
  searchMarketsByVector: jest.fn(() => Promise.resolve([
    { slug: 'test-market', similarity_score: 0.95 }
  ]))
}))
```

### OpenAI
```typescript
jest.mock('@/lib/openai', () => ({
  generateEmbedding: jest.fn(() => Promise.resolve(new Array(1536).fill(0.1)))
}))
```

## Best Practices

**DO:**
- Write the test FIRST, before any implementation
- Run tests and verify they FAIL before implementing
- Write minimal code to make tests pass
- Refactor only after tests are green
- Add edge cases and error scenarios
- Aim for 80%+ coverage (100% for critical code)

**DON'T:**
- Write implementation before tests
- Skip running tests after each change
- Write too much code at once
- Test implementation details (test behavior instead)
- Mock everything (prefer integration tests)

## Common Testing Mistakes

### Test behavior, not implementation
```typescript
// BAD: testing internal state
expect(component.state.count).toBe(5)

// GOOD: test what users see
expect(screen.getByText('Count: 5')).toBeInTheDocument()
```

### Use semantic selectors
```typescript
// BAD: brittle
await page.click('.css-class-xyz')

// GOOD: resilient
await page.click('button:has-text("Submit")')
```

### Keep tests independent
```typescript
// BAD: tests depend on each other
test('creates user', () => { /* ... */ })
test('updates same user', () => { /* needs previous test */ })

// GOOD: each test sets up its own data
test('updates user', () => {
  const user = createTestUser()
  // Test logic
})
```

## Test File Organization

```
src/
├── components/
│   └── Button/
│       ├── Button.tsx
│       └── Button.test.tsx          # Unit tests
├── app/api/
│   └── markets/
│       ├── route.ts
│       └── route.test.ts            # Integration tests
└── e2e/
    ├── markets.spec.ts              # E2E tests
    └── auth.spec.ts
```

## Coverage Thresholds

```json
{
  "jest": {
    "coverageThresholds": {
      "global": {
        "branches": 80,
        "functions": 80,
        "lines": 80,
        "statements": 80
      }
    }
  }
}
```

## Continuous Testing

```bash
npm test -- --watch           # Watch mode
npm test && npm run lint      # Pre-commit hook
npm test -- --coverage --ci   # CI/CD
```

## Integration with Other Commands

- Use `/plan` first to understand what to build
- Use `/tdd` to implement with tests
- Use `/build-and-fix` if build errors occur
- Use `/code-review` to review implementation
- Use `/e2e` for E2E testing
- Use `/test-coverage` to verify coverage

## Related Agent

This skill invokes the `tdd-guide` agent for hands-on TDD guidance.
