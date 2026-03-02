---
name: api-dto-patterns
description: API Request DTO patterns - Zod schema definition, file structure, naming conventions for Next.js and NestJS.
---

# API DTO Patterns

API endpoint 작업 시 Request 입력을 항상 Zod schema로 정의한다. Response는 대상 아님.

## Common Rules

### File Separation

Schema는 handler/route 파일과 분리하여 `dto.ts`에 정의한다.

### Naming Convention

- Schema: `{Action}{Resource}{Source}Schema` (e.g., `CreateUserBodySchema`)
- Source: `Body` | `Query` | `Params`
- Type: `z.infer<typeof schema>` → Schema 이름에서 "Schema" 제거 (e.g., `CreateUserBody`)

### Mandatory Rules

- body, query, params 각각 별도 schema 정의
- `z.infer<>` 로 타입 추출 필수 (수동 interface/type 정의 금지)
- `any`, `unknown` 직접 사용 금지 — 반드시 Zod를 거쳐야 함
- schema에서 `.transform()`, `.default()` 등 비즈니스 로직 금지 — validation만
- Prisma generated types (`Prisma.XxxCreateInput` 등)를 Request DTO로 사용 금지 — DTO는 항상 별도 Zod schema로 정의

## Next.js (App Router)

Route segment 옆에 `dto.ts` 배치 (colocation 원칙):

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

- Server Action에서도 동일하게 `dto.ts` 분리 적용
- `searchParams`는 `QuerySchema`로 validation

## NestJS

모듈 내 `dto/` 디렉토리에 schema별 파일 분리:

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

- class-validator 대신 Zod 사용 (일관성)
- NestJS Pipe 자동화 또는 명시적 `.parse()` 모두 허용
