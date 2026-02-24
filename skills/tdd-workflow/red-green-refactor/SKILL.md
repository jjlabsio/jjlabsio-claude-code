---
name: red-green-refactor
description: Core TDD cycle instructions for the tdd-workflow -- scaffold interfaces, write failing tests, implement minimal code, refactor
user-invocable: false
---

# Red-Green-Refactor Cycle

## Task

Guide the TDD practitioner through the Red-Green-Refactor cycle for each piece of functionality.

## Instructions

### 1. Scaffold Interfaces

Define types/interfaces for inputs and outputs before writing any code:

```typescript
// Define the contract first
interface SearchResult {
  slug: string
  name: string
  similarity_score: number
}

type SearchFn = (query: string) => Promise<SearchResult[]>
```

### 2. Write Failing Test (RED)

Write tests that FAIL because implementation doesn't exist yet:

```typescript
describe('searchMarkets', () => {
  it('returns semantically similar markets', async () => {
    const results = await searchMarkets('election')
    expect(results).toHaveLength(5)
    expect(results[0]).toMatchObject({ slug: expect.any(String) })
  })
})
```

### 3. Run Tests - Verify FAIL

```bash
npm test
# Test MUST fail -- if it passes, the test is not testing anything new
```

Confirm the failure is for the **right reason** (e.g., function not found, not a syntax error).

### 4. Implement Minimal Code (GREEN)

Write the absolute minimum code to make the test pass:

```typescript
export async function searchMarkets(query: string): Promise<SearchResult[]> {
  const embedding = await generateEmbedding(query)
  return await vectorSearch(embedding)
}
```

### 5. Run Tests - Verify PASS

```bash
npm test
# All tests must pass
```

### 6. Refactor (IMPROVE)

With green tests as a safety net:
- Remove duplication
- Improve naming
- Optimize performance
- Extract helpers if needed

Run tests again after refactoring to confirm they still pass.

## Expected Output

After each cycle:
- A failing test that defines the expected behavior
- Minimal implementation that makes the test pass
- Clean, refactored code with all tests still green

## Notes

- One cycle per function/behavior -- don't batch multiple behaviors
- If a test passes immediately, it's not testing new functionality
- Refactoring must not change behavior -- only structure
