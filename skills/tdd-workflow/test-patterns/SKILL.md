---
name: test-patterns
description: Test type selection, mocking strategies, edge cases, and anti-patterns for the tdd-workflow
user-invocable: false
---

# Test Patterns

## Task

Select the right test type and patterns based on what's being tested.

## Instructions

### Test Type Selection

| What you're testing | Test type | Framework |
|---|---|---|
| Pure functions, utilities | Unit | Jest/Vitest |
| React components | Unit | Testing Library |
| API endpoints | Integration | Jest + NextRequest |
| User flows | E2E | Playwright |

### Unit Tests

```typescript
describe('calculateSimilarity', () => {
  it('returns 1.0 for identical embeddings', () => {
    const v = [0.1, 0.2, 0.3]
    expect(calculateSimilarity(v, v)).toBe(1.0)
  })

  it('handles null gracefully', () => {
    expect(() => calculateSimilarity(null, [])).toThrow()
  })
})
```

### Integration Tests

```typescript
describe('GET /api/markets/search', () => {
  it('returns 200 with valid results', async () => {
    const req = new NextRequest('http://localhost/api/markets/search?q=test')
    const res = await GET(req, {})
    expect(res.status).toBe(200)
    expect((await res.json()).success).toBe(true)
  })

  it('returns 400 for missing query', async () => {
    const req = new NextRequest('http://localhost/api/markets/search')
    const res = await GET(req, {})
    expect(res.status).toBe(400)
  })
})
```

### E2E Tests

```typescript
test('user can search and view market', async ({ page }) => {
  await page.goto('/')
  await page.fill('input[placeholder="Search"]', 'election')
  await page.waitForTimeout(600)

  const results = page.locator('[data-testid="market-card"]')
  await expect(results).toHaveCount(5, { timeout: 5000 })
})
```

### Mocking External Dependencies

**Supabase:**
```typescript
jest.mock('@/lib/supabase', () => ({
  supabase: {
    from: jest.fn(() => ({
      select: jest.fn(() => ({
        eq: jest.fn(() => Promise.resolve({ data: mockData, error: null }))
      }))
    }))
  }
}))
```

**Redis:**
```typescript
jest.mock('@/lib/redis', () => ({
  searchMarketsByVector: jest.fn(() => Promise.resolve([
    { slug: 'test-1', similarity_score: 0.95 }
  ]))
}))
```

**OpenAI:**
```typescript
jest.mock('@/lib/openai', () => ({
  generateEmbedding: jest.fn(() => Promise.resolve(new Array(1536).fill(0.1)))
}))
```

### Edge Cases (Must Test)

1. **Null/Undefined** -- null inputs, missing fields
2. **Empty** -- empty arrays, empty strings
3. **Invalid types** -- wrong type passed
4. **Boundaries** -- min/max values
5. **Errors** -- network failures, database errors
6. **Special characters** -- Unicode, SQL characters

### Anti-Patterns

```typescript
// BAD: testing internal state
expect(component.state.count).toBe(5)
// GOOD: test user-visible behavior
expect(screen.getByText('Count: 5')).toBeInTheDocument()

// BAD: tests depend on each other
test('creates user', () => { /* ... */ })
test('updates same user', () => { /* needs previous */ })
// GOOD: each test is independent
test('updates user', () => {
  const user = createTestUser()
  // ...
})

// BAD: brittle selectors
await page.click('.css-class-xyz')
// GOOD: semantic selectors
await page.click('button:has-text("Submit")')
```

## Expected Output

- Correct test type chosen for each piece of functionality
- External dependencies properly mocked
- Edge cases identified and tested
- No anti-patterns in test code

## Notes

- Prefer integration tests over excessive mocking
- Test behavior, not implementation details
- Each test must be independent -- no shared mutable state
