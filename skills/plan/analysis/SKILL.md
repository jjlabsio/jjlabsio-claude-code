---
name: analysis
description: Requirements analysis, architecture review, and risk identification for the plan workflow
user-invocable: false
---

# Analysis

## Task

Analyze requirements and existing codebase to gather all information needed for an implementation plan.

## Instructions

### 1. Requirements Analysis

- Understand the feature request completely
- Identify success criteria
- List assumptions and constraints
- If requirements are ambiguous, list open questions

### 2. Architecture Review

- Analyze existing codebase structure (use Read, Grep, Glob)
- Identify affected components and files
- Review similar implementations for reusable patterns
- Map dependencies between components

### 3. Risk Identification

Assess each risk as HIGH/MEDIUM/LOW:
- Breaking changes to existing functionality
- Performance implications
- Security concerns (auth, PII, external APIs)
- External dependency risks

### 4. Red Flags to Check

- Large functions (>50 lines)
- Deep nesting (>4 levels)
- Duplicated code
- Missing error handling
- Hardcoded values
- Missing tests
- Performance bottlenecks

## Expected Output

A structured analysis containing:
- Clear requirements restatement
- List of affected files and components
- Dependency map
- Risk assessment with severity levels
- Red flags found in existing code

## Notes

- Be specific: use exact file paths, function names, variable names
- Consider both happy path and edge cases
- For refactors: identify code smells and technical debt before planning changes
