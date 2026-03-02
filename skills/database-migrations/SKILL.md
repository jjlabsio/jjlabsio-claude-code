---
name: database-migrations
description: Database migration safety rules and zero-downtime procedures for production schema changes.
---

# Database Migration Rules

Production migration safety rules not covered by Claude's default behavior.

## Safety Rules

- Never mix DDL (schema) and DML (data) in a single migration.
- Always add NOT NULL columns with a default — without one, it causes a full table rewrite + lock.
- Use `CREATE INDEX CONCURRENTLY` for indexes on large tables.
- Test on production-sized data before applying.

## Zero-Downtime: Expand-Contract Pattern

Breaking changes (column rename/type change, etc.) are performed in 3 phases:

```
Phase 1 — EXPAND:  Add new column (nullable/default), write to both old+new, backfill
Phase 2 — MIGRATE: Read from new, verify data consistency
Phase 3 — CONTRACT: Drop old column
```

**Remove code first, drop column in the next deployment.** Never reverse this order.
