# API DTO Patterns — Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** API 작업 시 Request DTO를 항상 Zod schema로 정의하도록 강제하는 skill 추가.

**Architecture:** 단일 skill (`skills/api-dto-patterns/SKILL.md`)에 공통 규칙 + Next.js + NestJS 환경별 섹션. 기존 `rules/typescript/coding-style.md`의 Zod 섹션은 이 스킬로 대체.

**Tech Stack:** Markdown (skill definition), Zod (validation library)

---

## Design (Reference)

## Decisions

- **범위**: Request DTO만 (body, query, params). Response는 대상 아님.
- **위치**: `skills/api-dto-patterns/SKILL.md` (단일 skill)
- **접근**: 공통 규칙 + Next.js 섹션 + NestJS 섹션
- **기존 규칙**: `rules/typescript/coding-style.md`의 "Use Zod" 한 줄은 이 스킬 참조로 대체

## Skill Structure

### 1. Common Rules

**File separation**: handler/route 옆에 `dto.ts` 파일 분리

**Naming convention**:
- Schema: `{Action}{Resource}{Source}Schema` (e.g., `CreateUserBodySchema`)
- Source: `Body` | `Query` | `Params`
- Type: `z.infer<typeof schema>` -> same name without "Schema" (e.g., `CreateUserBody`)

**Mandatory rules**:
- body, query, params 각각 별도 schema 정의
- `z.infer<>` 로 타입 추출 필수 (수동 타입 정의 금지)
- `any`, `unknown` 직접 사용 금지 -- Zod를 거쳐야 함
- schema에서 `.transform()`, `.default()` 등 비즈니스 로직 금지 -- validation만
- Prisma generated types (`Prisma.XxxCreateInput` 등)를 Request DTO로 사용 금지

### 2. Next.js (App Router)

**File structure**:
```
app/api/users/
  route.ts          # handler
  dto.ts            # Zod schemas + inferred types
```

**Specifics**:
- Route segment별 `dto.ts` 배치 (colocation 원칙)
- Server Action에서도 동일하게 `dto.ts` 분리 적용
- `searchParams`는 `QuerySchema`로 validation

### 3. NestJS

**File structure**:
```
src/users/
  users.controller.ts
  dto/
    create-user.dto.ts
    update-user.dto.ts
```

**Specifics**:
- `dto/` 디렉토리로 분리 (NestJS 컨벤션)
- class-validator 대신 Zod 사용 (일관성)
- NestJS Pipe 자동화 또는 명시적 `.parse()` 모두 허용

---

## Implementation Tasks

### Task 1: Create `skills/api-dto-patterns/SKILL.md`

**Files:**
- Create: `skills/api-dto-patterns/SKILL.md`

**Step 1: Create the skill file**

Create `skills/api-dto-patterns/SKILL.md` with the following content:

```markdown
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
```

**Step 2: Commit**

```bash
git add skills/api-dto-patterns/SKILL.md
git commit -m "feat: add api-dto-patterns skill"
```

---

### Task 2: Update `rules/typescript/coding-style.md`

**Files:**
- Modify: `rules/typescript/coding-style.md:10-23`

**Step 1: Replace the Input Validation section**

Replace the entire `## Input Validation` section (lines 10-23) with a reference to the new skill:

```markdown
## Input Validation

Use Zod for all API input validation. For detailed DTO patterns (file structure, naming, examples), see the `api-dto-patterns` skill.
```

This removes the inline code example (now in the skill) and keeps the rule file minimal.

**Step 2: Commit**

```bash
git add rules/typescript/coding-style.md
git commit -m "refactor: replace inline Zod example with api-dto-patterns skill reference"
```

---

### Task 3: Update `README.md`

**Files:**
- Modify: `README.md:25-37`

**Step 1: Add api-dto-patterns to the skills table**

In the skills table (line 29), add a new row for `api-dto-patterns`:

```markdown
| `api-dto-patterns` | API Request DTO — Zod schema 정의, 파일 구조, 네이밍 (Next.js / NestJS) |
```

Update the skill count in the heading from `7개` to `8개`.

**Step 2: Commit**

```bash
git add README.md
git commit -m "docs: add api-dto-patterns to README skills table"
```
