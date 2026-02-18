---
description: Work on the next unblocked Beads task for the current Linear issue
argument-hint: Optional Linear issue ID (infers from branch if omitted)
---

## Context

- Current branch: !`git branch --show-current`
- Open Beads tasks: !`bd list --status open --limit 20 2>/dev/null || echo "NO_BEADS_DB"`
- In-progress Beads tasks: !`bd list --status in_progress --limit 5 2>/dev/null || echo "NONE"`

## Instructions

You are working on the next unblocked Beads task for a Linear issue. Follow these 6 steps precisely.

### Step 1: Determine the issue ID

Determine which Linear issue to work on:

- **If `$ARGUMENTS` is provided and not empty:** use it as the issue ID (convert to UPPERCASE).
- **If `$ARGUMENTS` is empty or not provided:** parse the issue ID from the current branch name. The branch format is `<prefix>/<issue-id>-<slug>` (e.g., `feat/onc-5-add-audit-trail` -> `ONC-5`). Extract the issue ID portion and convert to UPPERCASE.
- **Mismatch check:** if `$ARGUMENTS` was provided AND a branch-based issue ID can also be parsed, compare them. If they differ, **warn the user** clearly:
  ```
  WARNING: Argument issue ID (<arg>) does not match branch issue ID (<branch>).
  Branch: <current branch>
  ```
  Ask the user to confirm which issue they want to work on before proceeding. Do NOT continue until the user confirms.

If neither `$ARGUMENTS` nor the branch yields a valid issue ID, tell the user and stop.

### Step 2: Read the plan

Read the plan document at `docs/plans/<ISSUE-ID>.md` (e.g., `docs/plans/ONC-5.md`) using the Read tool.

This plan provides full context: issue description, codebase context, implementation approach, tasks, and testing strategy. You will need this to understand what the task requires and how it fits into the broader implementation.

If the plan file does not exist, tell the user and suggest they run `/issue-start <ISSUE-ID>` first.

### Step 3: Find and claim the next task

Using the Beads task context above, determine which task to work on:

1. **If there is already an in-progress task** (from the in-progress list above): continue working on that task. Do NOT re-claim it.
2. **If there are no in-progress tasks**: pick the **first open, unblocked** task from the open tasks list. Claim it by running:
   ```bash
   bd update <task-id> --claim
   ```
3. **If there are no open tasks remaining**: inform the user that all tasks are complete and suggest running `/issue-finish` to wrap up the issue. Stop here.

A task is "blocked" if it has unresolved dependencies (other tasks that must complete first). The `bd list` output indicates blocked tasks — skip those and pick the first unblocked one.

### Step 4: Read task details

Get the full task details:

```bash
bd show <task-id>
```

Cross-reference the task description with the plan from Step 2 to understand:
- What exactly needs to be implemented
- Which files are involved
- How this task relates to other tasks in the plan
- Any dependencies or prerequisites

### Step 5: Implement the task

Now implement the task. Follow these guidelines:

- **Read relevant source files** before making changes — understand the existing code first.
- **Follow project patterns and conventions.** If the project has Claude Code skills (in `.claude/skills/`), reference them for guidance on backend, frontend, testing, database, security, architecture, code quality, and performance patterns.
- **Make focused changes** — only modify what the task requires.
- **Run relevant tests** after implementing. Fix any failures before presenting results.
  - If existing tests break, fix them.
  - If the task warrants new tests (check the plan's Testing Strategy), write them.
  - Run the specific test files related to your changes, not the entire suite.

### Step 6: Present results

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
