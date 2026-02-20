# claude-plugins

Custom Claude Code plugins for codebase analysis, skill generation, and workflow automation.

## Installation (inside Claude Code)

Run these commands **inside a Claude Code session** (they start with `/`):

```
/plugin marketplace add crod951/claude-plugins
/plugin install stackgen@crod951
```

> Individual plugins may have additional prerequisites that run in your **terminal** (e.g., `brew install`). See each plugin's README for details.

## Available Plugins

### stackgen (v1.0.0)

Analyzes codebases and generates tailored Claude Code skills. **11 specialized agents** with optimized context passing.

#### Features

- **Fast Detection** - Single stack-detector (Haiku) analyzes dependencies, configs, structure
- **Context Passing** - Detector findings passed to analyzers to avoid redundant reads
- **Tech Gating** - Only spawns analyzers for detected technologies
- **8-File Limits** - Each analyzer reads max 8 files for efficiency

#### Commands

| Command | Description |
|---------|-------------|
| `/stackgen:analyze` | Full codebase analysis and skill generation |
| `/stackgen:quick` | Quick tech stack overview |
| `/stackgen:refresh` | Update existing skills |
| `/stackgen:check` | Audit skills for issues |

#### Agents

**Detection (1):**
- `stack-detector` - Comprehensive dependency/config/pattern analysis (Haiku)

**Core Analyzers (4, always run):**
- `security-analyzer` - Security patterns and best practices
- `architecture-analyzer` - Code structure and organization
- `code-quality-analyzer` - Linting, formatting, types, dependencies
- `performance-analyzer` - Performance optimization

**Conditional Analyzers (6, gated by detection):**
- `frontend-analyzer` - UI framework patterns (React, Vue, Angular)
- `backend-analyzer` - Server patterns (APIs, Server Actions)
- `database-analyzer` - ORM/database patterns
- `testing-analyzer` - Unit, integration, E2E tests
- `devops-analyzer` - CI/CD, Docker, deployment
- `monitoring-analyzer` - Logging, error tracking, analytics

#### Generated Skills

```
.claude/skills/
├── security/
├── architecture/
├── code-quality/
├── performance/
├── frontend/       (if detected)
├── backend/        (if detected)
├── database/       (if detected)
├── testing/        (if detected)
├── devops/         (if detected)
└── monitoring/     (if detected)
```

---

### issue-lifecycle (v1.0.0)

Automates the full lifecycle of working on Linear issues — from planning through PR creation. Integrates **Linear**, **Beads task tracking**, and **GitHub PRs** with Conventional Commits.

#### Prerequisites

- [Linear MCP plugin](https://github.com/anthropics/claude-code-plugins) installed and configured
- [Beads CLI](https://github.com/steveyegge/beads) installed (`bd` command available)
- [GitHub CLI](https://cli.github.com/) installed and authenticated (`gh` command available)

#### Install

```bash
/plugin install issue-lifecycle@crod951
```

#### Commands

| Command | Description |
|---------|-------------|
| `/issue-start <ID>` | Fetch issue, create plan, Beads tasks, branch, update Linear |
| `/issue-task [ID]` | Work on next unblocked task, present for review |
| `/commit` | Semantic commit + close Beads task |
| `/issue-finish [ID]` | Push, create PR, update Linear, post completion comment |

#### Auto-Loop Flags

| Flag | Command | Description |
|------|---------|-------------|
| `--auto` | `/issue-task` | Loop through all tasks: implement, commit, next — automatically |
| `--finish` | `/issue-task` | Also push + PR + Linear update after last task. Requires `--auto` |
| `--on-failure=stop\|skip` | `/issue-task` | Halt on failure (default) or skip and continue. Requires `--auto` |
| `--auto` | `/issue-start` | Chain into auto-loop after plan approval and setup |
| `--no-confirm` | `/issue-start` | Skip plan approval pause. Requires `--auto` |
| `--base <branch>` | `/issue-finish` | Target branch for the PR. Auto-detects repo default if omitted |

```bash
# Fully autonomous, zero pauses
/issue-start ONC-5 --auto --no-confirm

# Auto-loop tasks only (manual start/finish)
/issue-task --auto

# Auto-loop + auto-finish
/issue-task --auto --finish --on-failure=skip
```

#### Features

- **Plan-first workflow** — researches your codebase and writes a plan document before any code changes
- **Review checkpoints** — pauses for your approval after planning and after each task implementation
- **Auto-loop mode** — `--auto` implements all tasks continuously with inline commits, no manual steps between tasks
- **Fully autonomous option** — `/issue-start --auto --no-confirm` runs the entire lifecycle with zero pauses
- **Branch naming** — auto-generates semantic branches from Linear labels (`feat/`, `fix/`, `chore/`, `docs/`)
- **Conventional Commits** — auto-detects commit type and formats as `type(ISSUE-ID): description`
- **Safety rails** — warns on uncommitted changes, incomplete tasks, and issue ID mismatches
- **Linear integration** — updates status to In Progress / In Review and posts completion comments

#### Workflow

**Manual (default):**

```
/issue-start ONC-5       # Fetch issue → research → plan → review → branch
    ↓
/issue-task               # Claim next task → implement → present for review
    ↓
/commit                   # Stage → commit → close Beads task
    ↓
  (repeat /issue-task + /commit for each task)
    ↓
/issue-finish             # Pre-flight checks → push → PR → update Linear
```

**Autonomous:**

```
/issue-start ONC-5 --auto --no-confirm    # Everything, zero pauses
```

#### Linear Status Lifecycle

```
Todo → In Progress (/issue-start) → In Review (/issue-finish) → Done (PR merge)
```

See the [full step-by-step guide](./plugins/issue-lifecycle/README.md) for a detailed walkthrough.

---

## Workflow

**stackgen:**
1. **Analyze project**: `/stackgen:analyze`
2. **Quick context**: `/stackgen:quick`
3. **After upgrades**: `/stackgen:refresh`
4. **Maintenance**: `/stackgen:check`

**issue-lifecycle (manual):**
1. **Start issue**: `/issue-start ONC-5`
2. **Work on tasks**: `/issue-task` (repeat)
3. **Commit work**: `/commit` (after review)
4. **Finish issue**: `/issue-finish`

**issue-lifecycle (autonomous):**
1. **Everything**: `/issue-start ONC-5 --auto --no-confirm`

## License

MIT
