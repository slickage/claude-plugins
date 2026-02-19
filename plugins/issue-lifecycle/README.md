# issue-lifecycle

Automates the full lifecycle of working on Linear issues — from planning through PR creation. Integrates Linear, Beads task tracking, and GitHub PRs with Conventional Commits.

## Installation

### 1. Install prerequisites

This plugin depends on three external tools. Install and configure each one before proceeding.

**Linear MCP plugin** — connects Claude Code to your Linear workspace

```bash
# Add the Anthropic plugin marketplace (if you haven't already)
/plugin marketplace add anthropics/claude-code-plugins

# Install the Linear plugin
/plugin install linear
```

After installing, follow the Linear plugin's setup instructions to authenticate with your Linear workspace.

**Beads CLI** — persistent task tracking across Claude Code sessions

```bash
npm install -g @anthropic/beads
```

Verify it's available:

```bash
bd --version
```

> Beads is initialized per-project automatically the first time you run `/issue-start`.

**GitHub CLI** — used to create pull requests from the terminal

```bash
brew install gh     # macOS
```

Then authenticate:

```bash
gh auth login
```

### 2. Install the plugin

```bash
# Add the marketplace
/plugin marketplace add crod951/claude-plugins

# Install issue-lifecycle
/plugin install issue-lifecycle@crod951
```

### 3. Verify

Start a new Claude Code session and confirm the commands are available:

```
/issue-start
/issue-task
/commit
/issue-finish
```

You should see descriptions for each command. You're ready to go.

## Commands

| Command | Description |
|---------|-------------|
| `/issue-start <ID>` | Fetch Linear issue, research codebase, write plan, create Beads tasks, checkout branch, update Linear to In Progress |
| `/issue-task [ID]` | Claim next unblocked Beads task, implement it, present for review. Infers issue ID from branch if omitted. |
| `/commit` | Semantic commit in Conventional Commits format (`type(ISSUE-ID): description`), closes active Beads task |
| `/issue-finish [ID]` | Push branch, create PR with `Closes <ID>`, update Linear to In Review, post completion comment |

### Auto-Loop Flags

Both `/issue-start` and `/issue-task` support flags for autonomous execution.

#### `/issue-task` flags

| Flag | Default | Description |
|------|---------|-------------|
| `--auto` | off | Loop through all unblocked tasks: implement, commit, and move to the next task automatically |
| `--finish` | off | Run `/issue-finish` logic (push, PR, Linear update) after the last task completes. Requires `--auto` |
| `--on-failure=stop\|skip` | `stop` | What to do when a task fails. `stop` halts the loop. `skip` reverts changes and continues to the next task. Requires `--auto` |

#### `/issue-start` flags

| Flag | Default | Description |
|------|---------|-------------|
| `--auto` | off | After plan approval and setup, chain into auto-loop mode (implements all tasks, commits each, and finishes the issue) |
| `--no-confirm` | off | Skip the plan approval pause and auto-approve. Requires `--auto` |

#### Examples

```bash
# Auto-loop all tasks (manual start/finish)
/issue-task --auto

# Auto-loop + auto-finish when all tasks are done
/issue-task --auto --finish

# Auto-loop, skip failing tasks instead of stopping
/issue-task --auto --on-failure=skip

# Fully autonomous from a single command (pauses for plan approval)
/issue-start ONC-5 --auto

# Fully autonomous, zero pauses
/issue-start ONC-5 --auto --no-confirm
```

## Step-by-Step Guide

This walkthrough takes you through a complete issue lifecycle using `ONC-5` as an example.

### Step 1: Start the issue

```
/issue-start ONC-5
```

This kicks off the planning phase. Claude will:

1. **Fetch the issue from Linear** — pulls the title, description, labels, and metadata
2. **Research your codebase** — searches for relevant files, models, services, and tests related to the issue
3. **Write a plan document** — saves a structured plan to `docs/plans/ONC-5.md` containing:
   - Issue description
   - Codebase context discovered during research
   - Implementation approach
   - Numbered task breakdown with file paths
   - Testing strategy
4. **Pause for your review** — you'll see a summary of the plan and be asked to approve it or request changes

> **This is a checkpoint.** Read the plan, discuss adjustments, and only approve once you're happy. The plan drives everything that follows.

Once approved, Claude will:

5. **Initialize Beads** (first time only) — sets up task tracking for the project
6. **Create Beads tasks** — one task per plan item, with dependencies between sequential steps
7. **Create a feature branch** — auto-named from the issue labels and title (e.g., `feat/onc-5-add-audit-trail`)
8. **Update Linear to "In Progress"**

You'll see a summary with your branch name, task list, and a prompt to start working.

### Step 2: Work on the next task

```
/issue-task
```

> You don't need to pass the issue ID — it's inferred from the branch name.

Claude will:

1. **Read the plan** from `docs/plans/ONC-5.md` for full context
2. **Find the next unblocked task** from the Beads task list and claim it
3. **Implement the task** — reading existing code, making focused changes, and running relevant tests
4. **Present the results** — a summary of files changed and test results

> **Claude will NOT commit or close the task.** This is your chance to review the implementation. Check the changes, ask questions, or request adjustments before moving on.

### Step 3: Commit the work

```
/commit
```

Once you're satisfied with the implementation, run `/commit`. Claude will:

1. **Detect the commit type** from the changes (`feat`, `fix`, `chore`, `refactor`, etc.)
2. **Stage the changed files** individually (never `git add .` — sensitive files are excluded)
3. **Create a Conventional Commits message** formatted as `type(ONC-5): description`
4. **Close the active Beads task**

You'll see a summary with the commit message and how many tasks remain.

### Step 4: Repeat for remaining tasks

Alternate between `/issue-task` and `/commit` until all tasks are complete:

```
/issue-task    →  implement  →  review  →  /commit
/issue-task    →  implement  →  review  →  /commit
/issue-task    →  implement  →  review  →  /commit
```

When `/issue-task` finds no remaining tasks, it will tell you to run `/issue-finish`.

> **Want to skip the manual loop?** Run `/issue-task --auto` to implement, commit, and advance through all tasks automatically. Add `--finish` to also push and create the PR when done. See [Auto-Loop Flags](#auto-loop-flags) for details.

### Step 5: Finish the issue

```
/issue-finish
```

Claude will run through pre-flight checks first:

- **Uncommitted changes?** You'll be told to run `/commit` first.
- **Incomplete tasks?** You'll see a warning with the list and can choose to proceed or go back.

If everything checks out, Claude will:

1. **Push the branch** to origin
2. **Create a Pull Request** with:
   - Title from the Linear issue
   - `Closes ONC-5` for auto-linking
   - Summary derived from commits and tasks
   - Completed tasks checklist
   - Test plan
3. **Update Linear to "In Review"**
4. **Post a completion comment** on the Linear issue with a structured summary and PR link

You'll get the PR URL — share it for code review. When the PR is merged, the Linear issue moves to "Done" automatically via the GitHub integration.

## Workflow at a Glance

**Manual (default):**

```
/issue-start ONC-5       # Plan + review + setup
    ↓
/issue-task               # Implement next task
    ↓
/commit                   # Commit + close task
    ↓
  (repeat until all tasks are done)
    ↓
/issue-finish             # Push + PR + Linear update
```

**Autonomous:**

```
/issue-start ONC-5 --auto --no-confirm    # Everything, zero pauses
```

Or with plan approval:

```
/issue-start ONC-5 --auto    # Pause for plan review, then auto-loop everything
```

## Features

- **Plan approval checkpoint**: `/issue-start` pauses after creating the plan so you can review and tweak before Beads tasks are created
- **Auto-loop mode**: `/issue-task --auto` implements all tasks continuously with inline commits — no manual intervention between tasks
- **Fully autonomous option**: `/issue-start ONC-5 --auto --no-confirm` runs the entire lifecycle (plan → implement → commit → PR) with zero pauses
- **Configurable failure handling**: `--on-failure=stop|skip` controls whether the auto-loop halts or skips tasks that fail
- **Branch naming**: Auto-generates semantic branches from Linear labels (`feat/`, `fix/`, `chore/`, `docs/`)
- **Conventional Commits**: `/commit` auto-detects commit type and formats as `type(ISSUE-ID): description`
- **Safety rails**: `/issue-finish` warns on uncommitted changes and incomplete tasks
- **Mismatch detection**: Commands warn if the explicit issue ID doesn't match the current branch
- **Completion comments**: Posts structured completion summaries to Linear with task checklists

## Linear Status Lifecycle

```
Todo → In Progress (/issue-start) → In Review (/issue-finish) → Done (PR merge)
```

## Per-Project Setup

Each project needs Beads initialized once. `/issue-start` handles this automatically on first run, using the project directory name as the Beads prefix.

Plan documents are written to `docs/plans/<ISSUE-ID>.md` as a paper trail.
