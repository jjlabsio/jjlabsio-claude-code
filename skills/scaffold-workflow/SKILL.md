---
name: scaffold-workflow
description: Generate the full Command → Agent → Skills architecture for a new Claude Code workflow. Use this skill when the user wants to create a new workflow, automate a multi-step task, set up a feature-specific agent, or scaffold the CAS pattern. Triggers on requests like "create a workflow for X", "set up an agent that does Y", "I need a command for Z", "build a CAS pattern", or "scaffold command agent skills".
allowed-tools: Read, Write, Bash
---

# Scaffold Workflow

Generate the full Command → Agent → Skills pattern — the recommended architecture for multi-step Claude Code workflows.

## Architecture

```
/command-name           ← entry point (user types this)
      ↓ Task tool
  agent-name            ← domain orchestrator
      ↓ preloaded skills
  skill-1  skill-2  …  ← focused domain knowledge, one per step
```

Each component has a distinct role:

- **Command**: Thin entry point. Collects user input, delegates to agent via Task tool. No business logic.
- **Agent**: Orchestrates the workflow sequentially using preloaded skills. Does the reasoning.
- **Skills**: Domain knowledge injected at agent startup. Each skill handles exactly one step.

## Interview

Before generating, gather these details. Infer from context when possible — only ask what's missing.

1. **Name**: Workflow identifier (e.g., `code-audit`, `deploy-staging`). Used for file names and `/command-name`.
2. **Purpose**: What does this workflow accomplish? (1–2 sentences)
3. **Steps**: What are the sequential steps? Each step becomes a skill.
4. **Arguments**: Does the command accept user input? (e.g., a file path, branch name, issue number)
5. **Tools**: What tools does the agent need? (Read, Write, Edit, Bash, WebFetch, Grep, Glob, etc.)
6. **Model**: How complex is the reasoning? (`haiku` for simple fetching, `sonnet` for analysis, `opus` for complex decisions)
7. **Memory**: Should the agent learn across sessions? (`user` for cross-project, `project` for team-shared, `local` for personal project-specific). When enabled, the agent accumulates learnings in its memory directory — the user periodically reviews the memory and curates valuable patterns back into the skills.
8. **Skill visibility**: Should any skills be user-invocable? Default is agent-internal only (`user-invocable: false`). Set `true` only for skills that make sense as standalone `/slash-commands` outside the workflow.

## File Generation

Create all files. Use `Bash` to create directories, `Write` for each file.

### 1. Command File

**Location**: `.claude/commands/<name>.md`

```markdown
---
description: <one-line description of what this command does>
model: haiku
---

# <Title> Command

<Brief description of what this command orchestrates.>

## Workflow

1. <If arguments needed: Use AskUserQuestion to collect [X] from the user>
2. Use the Task tool to invoke the <name> agent

## Agent Invocation

Use the Task tool to invoke the <name> agent:

- subagent_type: <name>
- description: <brief one-liner>
- prompt: <what to tell the agent — include any user-provided arguments here>
- model: <haiku|sonnet|opus>

Wait for the agent to complete its workflow.

## Output

Summarize the result to the user: <what to show>
```

**Command rules:**

- Always use Task tool — never bash commands to invoke agents
- Collect user input via `AskUserQuestion` before invoking the agent
- Use `haiku` model — commands are lightweight orchestrators, not thinkers
- Keep it under 40 lines; all logic belongs in the agent

### 2. Agent File

**Location**: `.claude/agents/<name>.md`

```markdown
---
name: <name>
description: <When to invoke. Start with "PROACTIVELY" only if the agent should auto-trigger.>
tools: <only the tools this agent actually needs>
model: <haiku|sonnet|opus>
color: <pick a color: green, blue, magenta, cyan, yellow>
skills:
  - <name>/<skill-1>
  - <name>/<skill-2>
---

# <Title> Agent

You are a specialized agent for <purpose>.

## Workflow

Execute these steps sequentially using your preloaded skills:

1. **Understand the Task**: If a handoff document from a previous agent is provided, use it as primary context. Then read relevant code to fill any gaps.
2. **<Step 1 title>**: Follow the `<skill-1>` skill instructions to <accomplish X>
3. **<Step 2 title>**: Follow the `<skill-2>` skill instructions to <accomplish Y>

## Final Report

After completing all steps, provide a summary:

- <Key result 1>
- <Key result 2>

If this agent is part of an orchestrated workflow (invoked with a handoff document), also produce a handoff document for the next agent:

\`\`\`
## HANDOFF: <name> -> [next-agent]

### Context
[Summary of what was done]

### Findings
[Key decisions or discoveries]

### Files Modified
[List of files created or changed]

### Open Questions
[Unresolved items for the next agent]

### Recommendations
[Suggested focus areas for the next step]
\`\`\`

## Critical Requirements

1. **Sequential Execution**: Complete each step before starting the next
2. **Use Your Skills**: The skill content is preloaded — follow those instructions precisely
3. **Data Flow**: Pass outputs from earlier steps as inputs to later steps
```

If the user chose memory in the interview, add `memory: <scope>` to the frontmatter and append this section to the agent body:

```markdown
## Memory

- **Before starting**: Review your memory for patterns and learnings from previous runs
- **After completing**: Record new discoveries, edge cases, and patterns worth remembering
- Keep memory entries concise — focus on reusable patterns, not session-specific details
- The user will periodically review your memory and curate valuable patterns back into your skills
```

**Agent rules:**

- List only tools the agent actually uses — principle of least privilege
- `skills:` field preloads full skill content at startup (not lazy-loaded)
- Model selection: `haiku` for simple fetch/write tasks, `sonnet` for analysis or multi-step reasoning, `opus` for complex architectural decisions
- `description` is used by Claude for auto-discovery — be specific about the domain
- **Skills vs Memory**: Skills are static domain knowledge (rules, templates, instructions). Memory is dynamic learnings accumulated over time (edge cases, discovered patterns). Skills define _how to do the work_; memory records _what was learned doing it_. The user curates memory into skills — not the agent.
- **Handoff support**: Always included by default as conditional — activates only when the agent is invoked within an orchestrated workflow (e.g., `commands/orchestrate.md`). Zero cost for standalone execution.

**Advanced frontmatter** — do not ask about these during the interview. Apply only when the user mentions a relevant need, or infer from context:

| Field             | When to use                                         | Example                                    |
| ----------------- | --------------------------------------------------- | ------------------------------------------ |
| `maxTurns`        | User wants to limit agent execution length          | `maxTurns: 15`                             |
| `permissionMode`  | Agent needs elevated or restricted permissions      | `acceptEdits`, `bypassPermissions`, `plan` |
| `background`      | Agent should always run as a background task        | `background: true`                         |
| `isolation`       | Agent needs an isolated copy of the repo            | `isolation: worktree`                      |
| `hooks`           | Agent needs lifecycle hooks (linting on edit, etc.) | `PostToolUse` with matcher                 |
| `mcpServers`      | Agent needs specific MCP servers                    | Server names or inline configs             |
| `disallowedTools` | Agent should be blocked from certain tools          | `disallowedTools: Bash, Write`             |

### 3. Skill Files

For each step, create: `.claude/skills/<name>/<step>/SKILL.md`

All skills for a workflow live under the same parent directory (`.claude/skills/<name>/`), keeping the skills tree organized by workflow rather than scattered as flat siblings.

```markdown
---
name: <step>
description: Instructions for <doing X> as part of the <name> workflow
user-invocable: false
---

# <Skill Title>

## Task

<What this skill accomplishes in one sentence.>

## Instructions

1. <Step 1>
2. <Step 2>
3. <Step 3>

## Expected Output

After following these instructions:
```

<Example of what the agent should produce or have done>
```

## Notes

- <Any constraints, edge cases, or important context>

```

**Skill rules:**
- One skill per step — keep each focused and atomic
- `description` field drives auto-discovery: be specific about purpose and context
- **Default to `user-invocable: false`** for CAS-pattern skills. In this pattern, skills are agent-internal domain knowledge — they don't make sense as standalone `/slash-commands`. Only set `true` if the skill is genuinely useful outside the workflow (e.g., a general-purpose utility skill)
- `allowed-tools`: Optionally restrict which tools the skill can use without permission prompts (e.g., `Bash(npm test *)`, `Bash(gh *)`)
- `disable-model-invocation: true` prevents Claude from auto-loading — use for skills that should only be invoked explicitly by the user via `/name`
- Supporting files (scripts, references, assets) can live in the same directory as `SKILL.md`

## After Generating

### 1. Summary Table

Show the user the generated files:

| Component | File | Role |
|-----------|------|------|
| Command | `.claude/commands/<name>.md` | Entry point — user runs `/<name>` |
| Agent | `.claude/agents/<name>.md` | Orchestrator |
| Skill: \<step1\> | `.claude/skills/<name>/<step1>/SKILL.md` | Step 1 |
| Skill: \<step2\> | `.claude/skills/<name>/<step2>/SKILL.md` | Step 2 |

### 2. Verify

Read back each generated file and confirm:
- Frontmatter is valid YAML (no syntax errors, correct field names)
- Agent `skills:` list matches the actual skill paths created
- Skill `description` fields reference the correct workflow name
- No placeholder text (`<name>`, `<step>`) left unsubstituted

### 3. Restart and Test

Tell the user:

> **Restart required**: New agents and commands require a Claude Code restart. Skills are loaded immediately.
> After restarting, run `/<name>` to verify the workflow end-to-end.

### 4. Compact Reminder

If the scaffolding consumed significant context (multiple skills generated), suggest:

> Consider running `/compact` to free up context before continuing with other work.

## Example

For a `"code-audit"` workflow with steps: scan → analyze → report

Generated files:
```

.claude/commands/code-audit.md
.claude/agents/code-audit.md
.claude/skills/code-audit/
├── scanner/SKILL.md # user-invocable: false
├── analyzer/SKILL.md # user-invocable: false
└── reporter/SKILL.md # user-invocable: false

````

Agent `skills:` field references them with the namespace prefix:
```yaml
skills:
  - code-audit/scanner
  - code-audit/analyzer
  - code-audit/reporter
````

Each skill frontmatter includes:

```yaml
---
name: scanner
description: Instructions for scanning code quality issues as part of the code-audit workflow
user-invocable: false
---
```

The skills are grouped under `code-audit/` so they don't clutter the top-level skills directory. They are hidden from the `/` menu since they only make sense when orchestrated by the code-audit agent. Users interact via `/code-audit`, not individual skills.
