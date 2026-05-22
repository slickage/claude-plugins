# issue-lifecycle

Standardizes issue tracking across **Linear and Jira**. Two skills:

- **Issue Intake** — turn requirements into a scaffolded main issue with 1:1 linked sub-issues and BEADS tasks, then offer to hand off to implementation.
- **Issue Lifecycle** — drive an existing issue from breakdown through implementation to an open PR, in one resumable autonomous pass.

Both share one provider abstraction, so the same workflow runs against Linear or Jira. BEADS is the source of truth; state syncs one way, BEADS → tracker.

## How it's structured

```
TRACKER                          BEADS
main issue           <--1:1-->   parent task (blocked by all children)
  sub-issue 1        <--1:1-->     child task 1
  sub-issue 2        <--1:1-->     child task 2
  sub-issue 3        <--1:1-->     child task 3
```

One child BEADS task per sub-issue. The parent task pairs with the main issue and closes last. Closing all children unblocks the parent → issue done.

## Installation

### 1. Prerequisites (terminal)

**Beads CLI** — persistent task tracking across sessions:

```bash
brew install beads        # or: curl -fsSL https://raw.githubusercontent.com/steveyegge/beads/main/scripts/install.sh | bash
bd version
```

> Beads is initialized per-project automatically on first run, using the project directory name as the prefix.

**GitHub CLI** — used to create pull requests:

```bash
brew install gh
gh auth login
```

### 2. Tracker MCP (inside Claude Code)

Install and authenticate the MCP for your tracker:

- **Linear** — `/plugin install linear`, then follow its auth steps.
- **Jira** — install and authenticate the official **Atlassian Remote MCP** server. (No Jira MCP ships with this plugin.)

### 3. Plugin (inside Claude Code)

```
/plugin marketplace add slickage/claude-plugins
/plugin install issue-lifecycle@slickage
```

## Configuration

Create `.issue-lifecycle.json` at the repo root to declare the active tracker. The provider is **never** guessed from issue-ID format.

```json
{
  "provider": "linear",
  "linear": { "teamKey": "ONC" },
  "jira": {
    "cloudId": "your-atlassian-cloud-id",
    "projectKey": "PROJ",
    "subtaskIssueType": "Sub-task",
    "states": { "inProgress": "In Progress", "inReview": "In Review", "done": "Done" }
  }
}
```

If the file is missing, the provider defaults to `linear`. If the configured provider's MCP isn't connected, the skills stop with a clear message rather than half-working.

## Skills

### Issue Intake

Triggers on requests like "turn these requirements into an issue", "scaffold an issue from this PRD", "break these requirements into tickets".

1. Resolve provider, gather requirements (args / referenced file / conversation), research the codebase.
2. Clarify **only if** requirements are too thin to break down.
3. Create the main issue, then create 1:1 sub-issues + parent/child BEADS tasks (the **scaffold**).
4. Show the scaffold and **ask** whether to run Issue Lifecycle now.

Intake does not implement, branch, or commit — it stops at a review-ready scaffold.

### Issue Lifecycle

Triggers on requests like "start ONC-5", "work on this issue", "take this issue to a PR", or naming an issue ID.

1. Resolve provider; determine the issue ID (argument or branch name).
2. Fetch the issue, research the codebase, ensure the feature branch and plan doc.
3. Ensure the breakdown + scaffold exists:
   - tasks already exist → skip;
   - human-authored sub-issues exist → **adopt** them 1:1;
   - nothing exists → **invent** the breakdown and create sub-issues.
4. Move the issue to In Progress, then loop: claim task → sub-issue In Progress → implement → test → commit → `bd close` → sub-issue Done.
5. When all children close: push, open the PR (`Closes <ID>`), move the issue to In Review, post a completion comment.

**Resumable single pass.** Every step is guarded by an observable artifact (branch / `bd list` / task `external-ref` / `gh pr`). Run it once; re-invoke to resume exactly where it stopped. **Zero mid-run confirmation** — inspect the scaffold and re-invoke to continue if you want a review point. On an unrecoverable test failure it **stops and holds**: changes kept, task left in-progress, reported.

## Status lifecycle

```
Backlog → In Progress (lifecycle start) → In Review (PR opened) → Done (PR merge)
```

Sub-issues mirror their BEADS task: claimed → In Progress, closed → Done.

## Plugin layout

```
skills/
  issue-intake/SKILL.md       # Skill B — requirements → scaffold
  issue-lifecycle/SKILL.md    # Skill A — issue → PR
  shared/
    config.md                 # .issue-lifecycle.json resolution
    providers.md              # provider contract (Linear MCP / Jira Atlassian MCP)
    breakdown-and-link.md     # breakdown + sub-issues + parent task (adopt/invent)
    commit-and-close.md       # conventional commit + bd close + sub-issue → Done
```

## Migration from 2.x

The four slash commands (`/issue-start`, `/issue-task`, `/commit`, `/issue-finish`) and their `--auto` / `--no-confirm` / `--finish` / `--on-failure` flags are **removed**. The full lifecycle is now one autonomous skill, and a separate Intake skill creates issues from requirements. Plans still live at `docs/plans/<ISSUE-ID>.md`.
