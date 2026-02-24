---
name: coverage-analysis
description: Coverage analysis, thresholds, and verification for the tdd-workflow
user-invocable: false
---

# Coverage Analysis

## Task

Verify test coverage meets thresholds and identify gaps.

## Instructions

### 1. Run Coverage

```bash
npm test -- --coverage
# or
pnpm test --coverage
```

### 2. Check Thresholds

Required minimums:
- Branches: 80%
- Functions: 80%
- Lines: 80%
- Statements: 80%

Critical code requires **100%**: financial calculations, authentication, security logic, core business rules.

### 3. Analyze Gaps

Read `coverage/coverage-summary.json` to identify files below threshold.

For each under-covered file:
1. Identify untested code paths
2. Write tests for uncovered branches and functions
3. Prioritize: error handling > edge cases > happy path variations
4. Re-run coverage to verify improvement

### 4. Configure Thresholds

Ensure project enforces coverage in config:

```json
{
  "jest": {
    "coverageThreshold": {
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

## Expected Output

- Coverage report showing all thresholds met
- Before/after metrics if gaps were filled
- List of any remaining gaps with justification

## Notes

- Coverage alone doesn't guarantee quality -- meaningful assertions matter
- Don't write tests just to hit numbers -- focus on testing real behavior
- Watch mode for continuous feedback: `npm test -- --watch --coverage`

### File Organization

```
src/
  components/
    Button/
      Button.tsx
      Button.test.tsx
  app/api/
    markets/
      route.ts
      route.test.ts
  e2e/
    markets.spec.ts
```
