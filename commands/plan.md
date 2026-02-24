---
description: Create implementation plan - restate requirements, assess risks, break down steps. WAIT for user CONFIRM before coding.
model: haiku
---

# Plan Command

Create a comprehensive implementation plan using the planner agent.

## Workflow

1. If the user hasn't specified what to plan, use AskUserQuestion to ask what feature, refactoring, or change to plan
2. Use the Task tool to invoke the planner agent

## Agent Invocation

Use the Task tool to invoke the planner agent:

- subagent_type: planner
- description: Create implementation plan for the specified task
- prompt: Include the user's description of what to plan, plus any relevant context or constraints
- model: opus

Wait for the agent to complete and present the plan.

## Output

Present the plan to the user and explicitly ask for confirmation:
- The full implementation plan
- "WAITING FOR CONFIRMATION: Proceed with this plan? (yes/no/modify)"
