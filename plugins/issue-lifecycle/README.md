# issue-lifecycle

Automates the full lifecycle of working on Linear issues — from planning through PR creation. Integrates Linear, Beads task tracking, and GitHub PRs with Conventional Commits.

## Prerequisites

- [Linear MCP plugin](https://github.com/anthropics/claude-code-plugins) installed and configured
- [Beads CLI](https://github.com/steveyegge/beads) installed (`bd` command available)
- [GitHub CLI](https://cli.github.com/) installed and authenticated (`gh` command available)

## Commands

| Command | Description |
|---------|-------------|
| `/issue-start <ID>` | Fetch Linear issue, research codebase, write plan, create Beads tasks, checkout branch, update Linear to In Progress |
| `/issue-task [ID]` | Claim next unblocked Beads task, implement it, present for review. Infers issue ID from branch if omitted. |
| `/commit` | Semantic commit in Conventional Commits format (`type(ISSUE-ID): description`), closes active Beads task |
| `/issue-finish [ID]` | Push branch, create PR with `Closes <ID>`, update Linear to In Review, post completion comment |

## Workflow

```
/issue-start ONC-5       # Plan + setup
    ↓
/issue-task               # Work on next task (repeat)
    ↓
/commit                   # Commit + close task (after review)
    ↓
/issue-finish             # PR + Linear update (when all tasks done)
```

## Features

- **Plan approval checkpoint**: `/issue-start` pauses after creating the plan so you can review and tweak before Beads tasks are created
- **Branch naming**: Auto-generates semantic branches from Linear labels (`feat/`, `fix/`, `chore/`, `docs/`)
- **Conventional Commits**: `/commit` auto-detects commit type and formats as `type(ISSUE-ID): description`
- **Safety rails**: `/issue-finish` warns on uncommitted changes and incomplete tasks
- **Mismatch detection**: Commands warn if the explicit issue ID doesn't match the current branch
- **FUL-4 style completion comments**: Posts structured completion summaries to Linear with task checklists

## Linear Status Lifecycle

```
Todo → In Progress (/issue-start) → In Review (/issue-finish) → Done (PR merge)
```

## Per-Project Setup

Each project needs Beads initialized once. `/issue-start` handles this automatically on first run, using the project directory name as the Beads prefix.

Plan documents are written to `docs/plans/<ISSUE-ID>.md` as a paper trail.
