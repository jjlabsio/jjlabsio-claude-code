---
paths:
  - "**/*.ts"
  - "**/*.tsx"
  - "**/*.js"
  - "**/*.jsx"
---
# TypeScript/JavaScript Coding Style

## Input Validation

Use Zod for schema-based validation:

```typescript
import { z } from 'zod'

const schema = z.object({
  email: z.string().email(),
  age: z.number().int().min(0).max(150)
})

const validated = schema.parse(input)
```

## Console.log

- No `console.log` statements in production code
- Use proper logging libraries instead
- See hooks for automatic detection
