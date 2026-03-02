---
paths:
  - "**/*.ts"
  - "**/*.tsx"
  - "**/*.js"
  - "**/*.jsx"
---
# TypeScript/JavaScript Coding Style

## Input Validation

Use Zod for all API input validation. For detailed DTO patterns (file structure, naming, examples), see the `api-dto-patterns` skill.

## Console.log

- No `console.log` statements in production code
- Use proper logging libraries instead
- See hooks for automatic detection
