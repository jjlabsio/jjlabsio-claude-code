---
name: backend-patterns
description: Backend architecture rules - project-specific decisions for error handling, caching, logging, and data access.
---

# Backend Architecture Rules

Project-specific architecture decisions not covered by Claude's default behavior.

## Error Handling

- Define a custom `ApiError` class that includes `statusCode`, `message`, and `isOperational`.
- Return input validation errors (Zod, etc.) as 400 with per-field errors in `details`.

## Caching

- Implement caching as a decorator pattern wrapping the Repository (CachedXxxRepository).
- Do not place cache logic in Service or Controller layers.
- Always implement cache invalidation methods alongside caching.

## Logging

- Assign a `requestId` to every request and include it in logs.
- Never include PII (personally identifiable information) in logs.

## Query Rules

- Always wrap multi-table changes in a transaction.
