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

| Agent | 설명 |
|-------|------|
| planner | 구현 계획 수립 및 작업 분해 |
| architect | 시스템 설계 및 아키텍처 의사결정 |
| tdd-guide | 테스트 주도 개발(TDD) 워크플로우 강제 |
| code-reviewer | 코드 품질 및 보안 리뷰 |
| security-reviewer | 보안 취약점 분석 (OWASP Top 10) |
| database-reviewer | PostgreSQL 쿼리 최적화 및 스키마 설계 |
| build-error-resolver | 빌드 오류 진단 및 수정 |
| e2e-runner | Playwright E2E 테스트 생성 및 실행 |
| refactor-cleaner | 불필요한 코드(dead code) 탐지 및 제거 |
| doc-updater | 문서 및 코드맵 동기화 |

### Commands

| Command | 설명 |
|---------|------|
| `/plan` | 구현 계획 생성 |
| `/tdd` | 테스트 주도 개발 워크플로우 |
| `/code-review` | 코드 품질 리뷰 |
| `/orchestrate` | 멀티 에이전트 순차 워크플로우 |
| `/build-fix` | 빌드 오류 수정 |
| `/e2e` | E2E 테스트 생성 및 실행 |
| `/refactor-clean` | 불필요한 코드 제거 |
| `/update-docs` | 문서 업데이트 |
| `/update-codemaps` | 코드맵 업데이트 |
| `/verify` | 검증 루프 실행 (빌드, 린트, 테스트, 보안) |
| `/test-coverage` | 테스트 커버리지 분석 |
| `/learn` | 세션에서 패턴 추출 |
| `/sessions` | 세션 히스토리 관리 |
| `/setup-pm` | 패키지 매니저 설정 |
| `/skill-create` | Git 히스토리에서 스킬 생성 |

### Skills

| Skill | 설명 |
|-------|------|
| coding-standards | TypeScript, JavaScript, React, Node.js 코딩 표준 |
| frontend-patterns | React, Next.js, 상태 관리, 성능 최적화 패턴 |
| backend-patterns | API 설계, 데이터베이스, 캐싱, Node.js 패턴 |
| api-design | REST API 리소스 네이밍, 페이지네이션, 에러 응답 |
| postgres-patterns | PostgreSQL 쿼리 최적화, 인덱싱, 스키마 설계 |
| database-migrations | Prisma, Drizzle, raw SQL 마이그레이션 패턴 |
| docker-patterns | Docker Compose, 네트워킹, 볼륨, 컨테이너 보안 |
| deployment-patterns | CI/CD, Docker, 헬스체크, 롤백 전략 |
| security-review | 인증, 사용자 입력 검증, 시크릿 관리, API 보안 |
| tdd-workflow | TDD 방법론 (RED-GREEN-REFACTOR) |
| e2e-testing | Playwright 패턴, Page Object Model, CI/CD 통합 |
| verification-loop | 배포 전 빌드, 린트, 테스트, 보안 검증 |
| continuous-learning | 세션에서 재사용 가능한 패턴 자동 추출 |
| strategic-compact | 논리적 구간에서 컨텍스트 압축 제안 |

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

플러그인 설치 후 자동으로 도구 이벤트에 연동됩니다. 별도 설정 불필요.

### PreToolUse (도구 실행 전)

| Hook | 동작 |
|------|------|
| Dev server blocker | tmux 밖에서 `npm run dev` 실행 차단 |
| Tmux reminder | 장시간 실행 명령에 tmux 사용 권장 |
| Git push reminder | `git push` 전 변경사항 리뷰 알림 |
| Doc file blocker | 불필요한 `.md`/`.txt` 파일 생성 차단 |
| Strategic compact | ~50회 도구 호출마다 `/compact` 제안 |

### PostToolUse (도구 실행 후)

| Hook | 동작 |
|------|------|
| PR logger | `gh pr create` 후 PR URL 기록 |
| Build analysis | 빌드 명령 후 백그라운드 분석 실행 |
| Prettier format | JS/TS 파일 편집 후 자동 포맷팅 |
| TypeScript check | `.ts`/`.tsx` 편집 후 `tsc --noEmit` 실행 |
| console.log warning | 편집된 파일에 `console.log` 포함 시 경고 |

### Lifecycle (생명주기)

| Hook | 동작 |
|------|------|
| Session start | 이전 컨텍스트 로드, 패키지 매니저 감지 |
| Pre-compact | 컨텍스트 압축 전 상태 저장 |
| Console.log audit | 응답마다 수정된 파일의 console.log 검사 |
| Session end | 세션 상태 영속화 |
| Pattern extraction | 세션에서 재사용 가능한 패턴 평가 및 추출 |

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
