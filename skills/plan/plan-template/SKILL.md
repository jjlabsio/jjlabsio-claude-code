---
name: plan-template
description: Plan formatting, step breakdown, and best practices for the plan workflow
user-invocable: false
---

# Plan Template

## Task

Structure analysis results into a comprehensive, actionable implementation plan.

## Scope Boundary

계획은 WHAT(기능)과 WHY(이유)만 다룬다. HOW(구현)는 tdd-guide가 skill을 참고하여 결정한다.

**계획에 절대 포함하지 않는 것:**
- 파일 경로 또는 디렉토리 트리 (예: `src/components/Foo.tsx`, `app/api/notes/route.ts`)
- 컴포넌트/함수/hook 이름 (예: `useMediaRecorder`, `RecorderButton`)
- API 엔드포인트 경로 또는 HTTP 메서드 (예: `POST /api/notes/process`, `GET /api/notes/:id`)
- 코드 패턴이나 구조 (예: Server Action, Repository Pattern)

이런 세부사항은 tdd-guide가 frontend-patterns, api-design, backend-patterns 등의 skill을 참고하여 결정한다. 계획에서 미리 지정하면 skill 참조가 무용지물이 된다.

## Instructions

### Step Breakdown Rules

- 기능 단위 설명 (WHAT) — 구현 패턴이나 코드 구조 대신 기능, 의존관계, 리스크에 집중
- 참고할 skill 명시 (Skill delegation) — HOW는 tdd-guide가 skill을 참고하여 결정
- Dependencies between steps explicitly stated
- Complexity estimated per step (High/Medium/Low)
- Group related changes into phases
- Order by dependencies, minimize context switching
- Each step should be independently verifiable

### Skill Delegation

구현 패턴은 tdd-guide가 skill을 참고하여 결정한다. 계획에서는 어떤 skill을 참고해야 하는지만 명시:

| 영역 | 위임 대상 Skill |
|------|----------------|
| React/Next.js 컴포넌트 구조 | jj:frontend-patterns |
| API 엔드포인트 설계 | jj:api-design |
| 서버사이드 코드 패턴 | jj:backend-patterns |
| 테스트 전략 상세 | tdd-workflow |

### Plan Format

```markdown
# Implementation Plan: [Feature Name]

## Overview
[2-3 sentence summary]

## Requirements
- [Requirement 1]
- [Requirement 2]

## Functional Components
- [기능 1: 어떤 기능이 필요한지 — 파일 경로/컴포넌트 이름 금지]
- [기능 2: 어떤 기능이 필요한지]

## Implementation Steps

### Phase 1: [Phase Name]
1. **[Step Name]**
   - What: 기능 설명
   - Why: 이유
   - Skill: 참고할 skill (HOW는 tdd-guide에 위임)
   - Dependencies: None / Step X
   - Risk: Low/Medium/High

### Phase 2: [Phase Name]
...

## Testing Strategy

### Interfaces & Contracts
- [Key interfaces to define before writing tests]
- [Input/output types for each public function]

### Test Priorities (in order)
1. [Critical path: what must work first]
2. [Core logic: business rules and calculations]
3. [Edge cases: error handling, boundaries]

### Test Breakdown
- Unit tests: [functions to test]
- Integration tests: [flows to test]
- E2E tests: [user journeys to test]

### External Dependencies to Mock
- [Service/library to mock and why]

### Critical Code (100% coverage required)
- [Security, auth, financial, or core business logic paths]

## Risks & Mitigations
- **Risk**: [Description]
  - Mitigation: [How to address]

## Success Criteria
- [ ] Criterion 1
- [ ] Criterion 2
```

### Self-Review (필수)

계획 작성 후 아래 항목을 검증. 하나라도 해당하면 수정:
- [ ] 파일 경로나 디렉토리 트리가 포함되어 있는가?
- [ ] 컴포넌트/함수/hook 이름을 지정했는가?
- [ ] API 엔드포인트 경로나 HTTP 메서드를 명시했는가?
- [ ] 코드 패턴이나 구조를 명시했는가?
→ 해당 항목을 기능 단위 설명으로 교체

### Best Practices

1. **Be Specific about WHAT and WHY**: 기능 요구사항, 의존관계, 리스크, 수락 기준을 구체적으로. 구현 패턴(컴포넌트 구조, API 컨벤션, 코드 패턴)은 skill에 위임
2. **Consider Edge Cases**: Error scenarios, null values, empty states
3. **Minimize Changes**: Extend existing code over rewriting
4. **Maintain Patterns**: Follow existing project conventions
5. **Enable Testing**: Structure changes to be easily testable
6. **Think Incrementally**: Each step should be verifiable
7. **Document Decisions**: Explain why, not just what

### Refactoring Plans

When the task is a refactor:
1. Identify code smells and technical debt
2. List specific improvements needed
3. Preserve existing functionality
4. Create backwards-compatible changes when possible
5. Plan for gradual migration if needed

## Expected Output

A complete plan following the format above, ending with:
"WAITING FOR CONFIRMATION: Proceed with this plan? (yes/no/modify)"

## Notes

- The plan must include Testing Strategy -- critical for handoff to tdd-guide
- Never start implementation without user confirmation
- Keep phases small enough to be independently reviewable
