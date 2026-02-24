---
name: plan-template
description: Plan formatting, step breakdown, and best practices for the plan workflow
user-invocable: false
---

# Plan Template

## Task

Structure analysis results into a comprehensive, actionable implementation plan.

## Instructions

### Step Breakdown Rules

- Clear, specific actions with file paths
- Dependencies between steps explicitly stated
- Complexity estimated per step (High/Medium/Low)
- Group related changes into phases
- Order by dependencies, minimize context switching
- Each step should be independently verifiable

### Plan Format

```markdown
# Implementation Plan: [Feature Name]

## Overview
[2-3 sentence summary]

## Requirements
- [Requirement 1]
- [Requirement 2]

## Architecture Changes
- [Change 1: file path and description]
- [Change 2: file path and description]

## Implementation Steps

### Phase 1: [Phase Name]
1. **[Step Name]** (File: path/to/file.ts)
   - Action: Specific action to take
   - Why: Reason for this step
   - Dependencies: None / Requires step X
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
- Unit tests: [functions to test, with file paths]
- Integration tests: [API endpoints/flows to test]
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

### Best Practices

1. **Be Specific**: Exact file paths, function names, variable names
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
