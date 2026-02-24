---
description: Run TDD workflow - write tests first, then implement
model: haiku
---

# TDD Command

Orchestrate test-driven development using the tdd-guide agent.

## Workflow

1. If the user hasn't specified what to build/test, use AskUserQuestion to ask what feature, function, or bug fix to work on
2. Use the Task tool to invoke the tdd-guide agent

## Agent Invocation

Use the Task tool to invoke the tdd-guide agent:

- subagent_type: tdd-guide
- description: TDD workflow for the specified task
- prompt: Include the user's description of what to build/test, plus any relevant file paths or context
- model: opus

Wait for the agent to complete its workflow.

## Output

Summarize the result to the user:
- What tests were written
- What implementation was created
- Coverage metrics achieved
