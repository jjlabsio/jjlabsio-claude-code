# jj - Claude Code Plugin

Claude Code plugin for jjlabsio. Production-ready agents, skills, hooks, commands, and rules for full-stack TypeScript development.

Based on [everything-claude-code](https://github.com/affaan-m/everything-claude-code), customized for Next.js full-stack workflow.

## Requirements

- Claude Code CLI v2.1.0+

```bash
claude --version
```

---

## Installation

### Step 1: Install the Plugin

```bash
# Add marketplace
/plugin marketplace add jjlabsio/jjlabsio-claude-code

# Install plugin
/plugin install jj@jjlabsio-claude-code
```

Or add directly to `~/.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "jjlabsio-claude-code": {
      "source": {
        "source": "github",
        "repo": "jjlabsio/jjlabsio-claude-code"
      }
    }
  },
  "enabledPlugins": {
    "jj@jjlabsio-claude-code": true
  }
}
```

This gives you instant access to all commands, agents, skills, and hooks.

### Step 2: Install Rules (Required)

Claude Code plugins cannot distribute `rules` automatically. Install them manually:

```bash
git clone https://github.com/jjlabsio/jjlabsio-claude-code.git
cd jjlabsio-claude-code

# Copy common rules (required)
cp -r rules/common ~/.claude/rules/common

# Copy TypeScript rules
cp -r rules/typescript ~/.claude/rules/typescript
```

> **Important:** Copy entire directories — do NOT flatten with `/*`. Common and TypeScript directories contain files with the same names. Flattening causes overwrites.

### Step 3: Configure Package Manager

```bash
# Via environment variable
export CLAUDE_PACKAGE_MANAGER=pnpm

# Via command
/setup-pm
```

Supports: npm, pnpm, yarn, bun. Auto-detects from lock files and package.json.

### Step 4: Verify Installation

```bash
# Check installed components
/plugin list jj@jjlabsio-claude-code
```

---

## What's Inside

| Component | Count |
|-----------|-------|
| Agents | 10 |
| Commands | 15 |
| Skills | 13 |
| Rules | common (8) + typescript (5) |
| Hooks | PreToolUse (5) + PostToolUse (5) + Lifecycle (5) |

### Agents

| Agent | Purpose |
|-------|---------|
| planner | Implementation planning and task breakdown |
| architect | System design and architectural decisions |
| tdd-guide | Test-driven development enforcement |
| code-reviewer | Code quality and security review |
| security-reviewer | Vulnerability analysis (OWASP Top 10) |
| database-reviewer | PostgreSQL query optimization and schema design |
| build-error-resolver | Build error diagnosis and fix |
| e2e-runner | Playwright E2E test generation and execution |
| refactor-cleaner | Dead code detection and removal |
| doc-updater | Documentation and codemap sync |

### Commands

| Command | Description |
|---------|-------------|
| `/plan` | Create implementation plan |
| `/tdd` | Test-driven development workflow |
| `/code-review` | Code quality review |
| `/orchestrate` | Multi-agent sequential workflow |
| `/build-fix` | Fix build errors |
| `/e2e` | Generate and run E2E tests |
| `/refactor-clean` | Remove dead code |
| `/update-docs` | Update documentation |
| `/update-codemaps` | Update codemaps |
| `/verify` | Run verification loop |
| `/test-coverage` | Analyze test coverage |
| `/learn` | Extract patterns from session |
| `/sessions` | Manage session history |
| `/setup-pm` | Configure package manager |
| `/skill-create` | Generate skills from git history |

### Skills

| Skill | Description |
|-------|-------------|
| coding-standards | TypeScript, JavaScript, React, Node.js standards |
| frontend-patterns | React, Next.js, state management, performance |
| backend-patterns | API design, database, caching, Node.js patterns |
| api-design | REST API resource naming, pagination, error responses |
| postgres-patterns | PostgreSQL query optimization, indexing, schema design |
| database-migrations | Migration patterns for Prisma, Drizzle, and raw SQL |
| docker-patterns | Docker Compose, networking, volumes, container security |
| deployment-patterns | CI/CD, Docker, health checks, rollback strategies |
| security-review | Authentication, user input, secrets, API security |
| tdd-workflow | TDD methodology (RED-GREEN-REFACTOR) |
| e2e-testing | Playwright patterns, Page Object Model, CI/CD |
| verification-loop | Build, lint, test, security verification before deploy |
| continuous-learning | Auto-extract patterns from sessions |
| strategic-compact | Context compaction at logical breakpoints |

---

## Common Workflows

### Starting a New Feature

```
/plan "Add user authentication with OAuth"   -> planner creates implementation blueprint
/tdd                                          -> tdd-guide enforces write-tests-first
/code-review                                  -> code-reviewer checks your work
```

Or use the orchestrated workflow:

```
/orchestrate feature "Add user authentication with OAuth"
```

### Fixing a Bug

```
/tdd                                          -> write a failing test that reproduces it
                                              -> implement the fix, verify test passes
/code-review                                  -> catch regressions
```

### Preparing for Production

```
/verify                                       -> run full verification loop
/e2e                                          -> critical user flow tests
/test-coverage                                -> verify 80%+ coverage
```

### Multi-Agent Orchestration

`/orchestrate` chains agents sequentially with handoff documents:

```
/orchestrate feature "Add search"    -> planner -> tdd-guide -> code-reviewer -> security-reviewer
/orchestrate bugfix "Fix login"      -> tdd-guide -> code-reviewer
/orchestrate refactor "Split module" -> architect -> planner -> code-reviewer
/orchestrate security "Audit auth"   -> security-reviewer -> architect
```

---

## Hooks

Hooks run automatically on tool events. No configuration needed after plugin install.

### PreToolUse (before tool execution)

| Hook | What It Does |
|------|-------------|
| Dev server blocker | Blocks `npm run dev` outside tmux |
| Tmux reminder | Suggests tmux for long-running commands |
| Git push reminder | Review changes before `git push` |
| Doc file blocker | Blocks random `.md`/`.txt` creation |
| Strategic compact | Suggests `/compact` every ~50 tool calls |

### PostToolUse (after tool execution)

| Hook | What It Does |
|------|-------------|
| PR logger | Logs PR URL after `gh pr create` |
| Build analysis | Background analysis after build commands |
| Prettier format | Auto-formats JS/TS files after edits |
| TypeScript check | Runs `tsc --noEmit` after `.ts`/`.tsx` edits |
| console.log warning | Warns about `console.log` in edited files |

### Lifecycle

| Hook | What It Does |
|------|-------------|
| Session start | Loads previous context, detects package manager |
| Pre-compact | Saves state before context compaction |
| Console.log audit | Checks modified files after each response |
| Session end | Persists session state |
| Pattern extraction | Evaluates session for reusable patterns |

### Disabling a Hook

Override in `~/.claude/settings.json`:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write",
        "hooks": [],
        "description": "Override: allow all .md file creation"
      }
    ]
  }
}
```

---

## Token Optimization

### Recommended Settings

Add to `~/.claude/settings.json`:

```json
{
  "model": "sonnet",
  "env": {
    "MAX_THINKING_TOKENS": "10000",
    "CLAUDE_AUTOCOMPACT_PCT_OVERRIDE": "50"
  }
}
```

| Setting | Default | Recommended | Impact |
|---------|---------|-------------|--------|
| `model` | opus | **sonnet** | ~60% cost reduction |
| `MAX_THINKING_TOKENS` | 31,999 | **10,000** | ~70% reduction in thinking cost |
| `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` | 95 | **50** | Compacts earlier, better quality in long sessions |

### Model Selection

| Model | When to Use |
|-------|-------------|
| `/model sonnet` | Default for most tasks |
| `/model opus` | Complex architecture, deep reasoning |
| `/model haiku` | Lightweight agents, frequent invocation |

### Context Management

- `/clear` between unrelated tasks (free, instant reset)
- `/compact` at logical breakpoints (after research, before implementation)
- `/cost` to monitor token spending
- Keep under 10 MCPs enabled, under 80 tools active

---

## Project Structure

```
jjlabsio-claude-code/
├── .claude-plugin/
│   └── plugin.json          # Plugin manifest
├── agents/                  # 10 specialized subagents
├── commands/                # 15 slash commands
├── skills/                  # 13 workflow definitions
├── rules/
│   ├── common/              # 8 language-agnostic rules
│   └── typescript/          # 5 TypeScript-specific rules
├── hooks/
│   ├── hooks.json           # Hook configuration
│   └── README.md            # Hook documentation
└── scripts/
    ├── hooks/               # 9 hook script implementations
    └── lib/                 # Shared utilities
```

---

## Credits

Based on [everything-claude-code](https://github.com/affaan-m/everything-claude-code) by [Affaan Mustafa](https://x.com/affaanmustafa).

## License

MIT
