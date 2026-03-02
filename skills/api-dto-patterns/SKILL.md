---
name: api-dto-patterns
description: API Request DTO patterns - Zod schema definition, file structure, naming conventions for Next.js and NestJS.
---

# API DTO Patterns

Always define Request inputs as Zod schemas when working on API endpoints. Response is not in scope.

## Common Rules

### File Separation

Define schemas in a separate `dto.ts` file, not in the handler/route file.

### Naming Convention

- Schema: `{Action}{Resource}{Source}Schema` (e.g., `CreateUserBodySchema`)
- Source: `Body` | `Query` | `Params`
- Type: `z.infer<typeof schema>` — Remove "Schema" from the schema name (e.g., `CreateUserBody`)

### Mandatory Rules

- Define separate schemas for body, query, and params
- Always extract types with `z.infer<>` (no manual interface/type definitions)
- Never use `any` or `unknown` directly — always validate through Zod
- No business logic in schemas (e.g., `.transform()`) — `.default()` and `.coerce` are allowed only for query/params
- Never use Prisma generated types (`Prisma.XxxCreateInput`, etc.) as Request DTOs — always define DTOs as separate Zod schemas

## Next.js (App Router)

Place `dto.ts` next to the route segment (colocation principle):

```
app/api/users/
  route.ts          # handler
  dto.ts            # Zod schemas + inferred types
```

```typescript
// dto.ts
import { z } from 'zod'

export const CreateUserBodySchema = z.object({
  email: z.string().email(),
  name: z.string().min(1),
})
export type CreateUserBody = z.infer<typeof CreateUserBodySchema>

export const ListUsersQuerySchema = z.object({
  page: z.coerce.number().int().min(1).default(1),
  limit: z.coerce.number().int().min(1).max(100).default(20),
})
export type ListUsersQuery = z.infer<typeof ListUsersQuerySchema>

// route.ts
import { CreateUserBodySchema } from './dto'

export async function POST(req: Request) {
  const body = CreateUserBodySchema.parse(await req.json())
}
```

- Apply the same `dto.ts` separation for Server Actions
- Validate `searchParams` with `QuerySchema`

## NestJS

Separate schema files in the `dto/` directory within the module:

```
src/users/
  users.controller.ts
  dto/
    create-user.dto.ts
    update-user.dto.ts
```

```typescript
// dto/create-user.dto.ts
import { z } from 'zod'

export const CreateUserBodySchema = z.object({
  email: z.string().email(),
  name: z.string().min(1),
})
export type CreateUserBody = z.infer<typeof CreateUserBodySchema>

// users.controller.ts
import { CreateUserBodySchema } from './dto/create-user.dto'

@Post()
create(@Body() body: unknown) {
  const validated = CreateUserBodySchema.parse(body)
}
```

- Use Zod instead of class-validator (consistency)
- Both NestJS Pipe automation and explicit `.parse()` are allowed
