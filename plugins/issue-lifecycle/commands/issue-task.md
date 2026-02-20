---
description: Work on the next unblocked Beads task for the current Linear issue
argument-hint: "[ISSUE-ID] [--auto] [--finish] [--on-failure=stop|skip]"
---

## Context

- Current branch: !`git branch --show-current`
- Open Beads tasks: !`bd list --status open --limit 20 2>/dev/null || echo "NO_BEADS_DB"`
- In-progress Beads tasks: !`bd list --status in_progress --limit 5 2>/dev/null || echo "NONE"`

## Instructions

You are working on Beads tasks for a Linear issue. Follow these steps precisely.

### Step 1: Parse arguments and flags

Parse `$ARGUMENTS` to extract:

- **Issue ID:** Any non-flag argument (e.g., `ONC-5`). Optional — inferred from branch if omitted.
- **`--auto`:** If present, enable auto-loop mode (implement all tasks continuously with inline commits).
- **`--finish`:** If present (requires `--auto`), run issue-finish logic after the last task completes.
- **`--on-failure=stop|skip`:** Failure handling mode. Default is `stop`. Only relevant when `--auto` is set.

Examples:
- `/issue-task` → manual mode, infer issue ID from branch
- `/issue-task ONC-5` → manual mode, explicit issue ID
- `/issue-task --auto` → auto-loop, infer issue ID, stop on failure
- `/issue-task --auto --finish --on-failure=skip` → auto-loop, auto-finish, skip failures

### Step 2: Determine the issue ID

Determine which Linear issue to work on:

- **If an issue ID was parsed from arguments:** use it (convert to UPPERCASE).
- **If no issue ID in arguments:** parse from the current branch name. The branch format is `<prefix>/<issue-id>-<slug>` (e.g., `feat/onc-5-add-audit-trail` -> `ONC-5`). Extract the issue ID portion and convert to UPPERCASE.
- **Mismatch check:** if both an argument issue ID and branch issue ID exist and they differ, **warn the user** clearly:
  ```
  WARNING: Argument issue ID (<arg>) does not match branch issue ID (<branch>).
  Branch: <current branch>
  ```
  Ask the user to confirm which issue they want to work on before proceeding. Do NOT continue until the user confirms.

If neither arguments nor the branch yields a valid issue ID, tell the user and stop.

### Step 3: Read the plan

Read the plan document at `docs/plans/<ISSUE-ID>.md` (e.g., `docs/plans/ONC-5.md`) using the Read tool.

This plan provides full context: issue description, codebase context, implementation approach, tasks, and testing strategy. You will need this to understand what each task requires and how it fits into the broader implementation.

If the plan file does not exist, tell the user and suggest they run `/issue-start <ISSUE-ID>` first.

---

## Manual Mode (no `--auto` flag)

If `--auto` was NOT set, execute the following steps for a single task, then stop.

### Step 4M: Find and claim the next task

Using the Beads task context above, determine which task to work on:

1. **If there is already an in-progress task** (from the in-progress list above): continue working on that task. Do NOT re-claim it.
2. **If there are no in-progress tasks**: pick the **first open, unblocked** task from the open tasks list. Claim it by running:
   ```bash
   bd update <task-id> --claim
   ```
3. **If there are no open tasks remaining**: inform the user that all tasks are complete and suggest running `/issue-finish` to wrap up the issue. Stop here.

A task is "blocked" if it has unresolved dependencies (other tasks that must complete first). The `bd list` output indicates blocked tasks — skip those and pick the first unblocked one.

### Step 5M: Read task details

Get the full task details:

```bash
bd show <task-id>
```

Cross-reference the task description with the plan from Step 3 to understand:
- What exactly needs to be implemented
- Which files are involved
- How this task relates to other tasks in the plan
- Any dependencies or prerequisites

### Step 6M: Implement the task

Now implement the task. Follow these guidelines:

- **Read relevant source files** before making changes — understand the existing code first.
- **Follow project patterns and conventions.** If the project has Claude Code skills (in `.claude/skills/`), reference them for guidance on backend, frontend, testing, database, security, architecture, code quality, and performance patterns.
- **Make focused changes** — only modify what the task requires.
- **Run relevant tests** after implementing. Fix any failures before presenting results.
  - If existing tests break, fix them.
  - If the task warrants new tests (check the plan's Testing Strategy), write them.
  - Run the specific test files related to your changes, not the entire suite.

### Step 7M: Present results

Once implementation is complete, present a summary:

```
--- Task Progress ---

  Issue:    <ISSUE-ID>
  Task:     <task-id> — <task title>
  Status:   Implemented (pending review)

  Changes:
    - <file path>: <what changed>
    - <file path>: <what changed>
    ...

  Tests:
    <test results summary — passed/failed/skipped>

  Next: Review the changes, then run /commit to commit and close the task.

---------------------
```

**CRITICAL: Do NOT commit changes. Do NOT mark the Beads task as closed/done.** The user will review the changes first and use `/commit` to handle committing and task closure.

---

## Auto-Loop Mode (`--auto` flag)

If `--auto` was set, execute the following loop to implement ALL unblocked tasks continuously with inline commits.

Initialize tracking variables:
- `completed_tasks` = empty list
- `skipped_tasks` = empty list
- `total_task_count` = count of all open tasks from context above

### LOOP START

#### Step 4A: Check for remaining tasks

Run:
```bash
bd list --status open --limit 20
```

- **If no open tasks remain:** exit the loop, go to **Post-Loop**.
- **If there is an in-progress task:** continue with that task.
- **If there are open, unblocked tasks:** claim the first one:
  ```bash
  bd update <task-id> --claim
  ```
- **If all remaining tasks are blocked** (dependencies on incomplete/skipped tasks): exit the loop, go to **Post-Loop**.

#### Step 5A: Read task details

```bash
bd show <task-id>
```

Cross-reference with the plan to understand requirements, files involved, and dependencies.

#### Step 6A: Implement the task

Follow the same implementation guidelines as manual mode:

- Read relevant source files before making changes.
- Follow project patterns and conventions.
- Make focused changes — only what the task requires.
- Run relevant tests after implementing. Fix any failures.
  - If existing tests break, fix them.
  - If the task warrants new tests, write them.
  - Run specific test files, not the entire suite.

**Evaluate outcome:**

- **Tests pass and implementation is complete** → proceed to Step 7A (commit).
- **Tests fail and cannot be fixed after reasonable effort, or implementation is stuck** →
  - If `--on-failure=stop`: show what was attempted, present the auto-loop final summary with results so far, and **STOP**. Do NOT continue the loop.
  - If `--on-failure=skip`: revert uncommitted changes for this task (`git checkout -- .`), add task to `skipped_tasks` with reason, and **LOOP BACK** to Step 4A.

#### Step 7A: Stage, commit, and close the task

This step inlines the commit logic. Follow these rules precisely:

**7A.1 — Parse issue ID for commit scope:**
Use the issue ID from Step 2 (already UPPERCASE).

**7A.2 — Determine commit type:**
Analyze the `git diff HEAD` output. Choose exactly ONE type:
- `feat` — New feature or user-facing functionality
- `fix` — Bug fix
- `chore` — Config, dependencies, build, tooling
- `docs` — Documentation only
- `refactor` — Code restructuring, no behavior change
- `test` — Adding or updating tests only
- `perf` — Performance improvement
- `style` — Formatting, whitespace only

When in doubt between `feat` and `refactor`, prefer `feat` if there is any user-facing behavior change.

**7A.3 — Draft commit message:**
Format: `type(ISSUE-ID): concise description`

Rules:
- Type is lowercase
- Issue ID is UPPERCASE inside parentheses
- Description is lowercase, imperative mood ("add", "fix", "update")
- No period at end
- Total message under 72 characters
- Be specific (e.g., "add patient export button" not "update UI")

**7A.4 — Stage and commit:**
Stage files individually or by directory. **NEVER** use `git add -A` or `git add .`. NEVER stage files matching: `.env*`, `credentials*`, `*secret*`, `*.pem`, `*.key`, `config/master.key`, `config/credentials.yml.enc`.

```bash
git add <file1> <file2> ...
```

Commit using HEREDOC format:
```bash
git commit -m "$(cat <<'EOF'
type(ISSUE-ID): description
EOF
)"
```

**7A.5 — Close the Beads task:**
```bash
bd close <task-id>
```

Add the task to `completed_tasks` with its commit message.

#### Step 8A: Show progress summary

After each task completes, display:

```
--- Task N/T Complete ───────────────────
  Task:      <task-id> — <task title>
  Commit:    <full commit message>
  Tests:     <passed count> passed, <failed count> failed
  Changed:   <file count> files
  Remaining: <open task count> tasks
─────────────────────────────────────────
```

Where N = number of tasks completed so far, T = total_task_count.

#### LOOP BACK to Step 4A

---

### Post-Loop

#### Step 9A: Show final summary

```
--- Auto-Loop Complete ─────────────────
  Issue:     <ISSUE-ID>
  Completed: <count>/<total> tasks
  Skipped:   <count> task(s)
  Commits:   <count>

  Completed tasks:
    ✓ <task-id> — <commit message>
    ✓ <task-id> — <commit message>
    ...

  Skipped tasks: (if any)
    ✗ <task-id> — <task title> (<reason>)
    ...

  Next: /issue-finish
─────────────────────────────────────────
```

#### Step 10A: Auto-finish (if `--finish` flag is set)

If `--finish` was set, proceed to finish the issue. Execute the following steps (these mirror `/issue-finish`):

**10A.1 — Pre-flight checks:**
- Check `git status` — if there are uncommitted changes, STOP and warn.
- Check `bd list --status open` — if there are open tasks (that were not skipped), warn and ask user to confirm.

**10A.2 — Fetch the Linear issue:**
Use `mcp__plugin_linear_linear__get_issue` with id: `<ISSUE-ID>`.

**10A.3 — Detect the base branch:**
```bash
gh repo view --json defaultBranchRef --jq '.defaultBranchRef.name'
```
Use the result as the base branch for the PR (falls back to `main` if the command fails).

**10A.4 — Push the branch:**
```bash
git push -u origin <branch-name>
```

**10A.5 — Create the Pull Request:**
```bash
gh pr create --base <base-branch> --title "<Linear issue title>" --body "$(cat <<'EOF'
Closes <ISSUE-ID>

## Summary

- <bullet 1 — derived from commits and completed tasks>
- <bullet 2>
- <bullet 3 if applicable>

## Linear Issue

<Linear issue URL>

## Completed Tasks

- [x] <task-1-title>
- [x] <task-2-title>
- [ ] <skipped-task-title (if any)>

## Test Plan

- <testing step 1>
- <testing step 2>
EOF
)"
```

Capture the PR URL.

**10A.6 — Update Linear status:**
Use `mcp__plugin_linear_linear__update_issue` with:
- id: `<issue UUID>`
- state: "In Review"

**10A.7 — Post completion comment on Linear:**
Use `mcp__plugin_linear_linear__create_comment` to post a comment on the issue with:

```
✅ **Completed**

All implementation tasks have been completed:

### Completed Tasks (beads: <beads-prefix>)
- ✅ <task-1-title>
- ✅ <task-2-title>
- ...

### Skipped Tasks (if any)
- ⚠️ <skipped-task-title> (<reason>)

### Implementation Summary
- <bullet 1 — brief description of what was implemented>
- <bullet 2>

PR: <PR-URL>
```

**10A.8 — Show finish summary:**

```
--- Issue Finished (Auto) ───────────────
  Issue:    <ISSUE-ID> — <Title>
  PR:       <PR-URL>
  Linear:   In Review
  Tasks:    <completed>/<total> completed

  The Linear issue will automatically move to Done
  when the PR is merged (via GitHub integration).

  Next: Share the PR link for code review.
─────────────────────────────────────────
```

If `--finish` was NOT set, the final summary from Step 9A is the last output.
