# API DTO Patterns Skill Design

## Summary

API 작업 시 Request DTO를 항상 Zod schema로 정의하도록 강제하는 skill 추가.
Next.js (App Router)와 NestJS 환경별 구조를 구분하여 제공.

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
