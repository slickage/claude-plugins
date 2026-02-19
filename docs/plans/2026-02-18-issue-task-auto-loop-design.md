# Issue-Task Auto-Loop Design

## Problem

The current `/issue-task` workflow requires manual invocation for each task cycle: `/issue-task` → review → `/commit` → repeat. Users want the option to run this autonomously.

## Goals

1. Add `--auto` flag to `/issue-task` to continuously loop through all unblocked tasks: claim → implement → stage → commit → next
2. Add `--auto` flag to `/issue-start` to chain into the auto-loop after planning, enabling a fully autonomous flow from a single command
3. Maintain the existing manual workflow as the default behavior

## Flags

### `/issue-task`

| Flag | Default | Description |
|------|---------|-------------|
| `--auto` | off | Loop through all unblocked tasks: implement, stage, commit, next |
| `--finish` | off | Run `/issue-finish` logic after the last task completes |
| `--on-failure=stop\|skip` | `stop` | What to do when a task fails |

### `/issue-start`

| Flag | Default | Description |
|------|---------|-------------|
| `--auto` | off | After plan approval, chain into `/issue-task --auto --finish` |
| `--no-confirm` | off | Skip plan approval pause (requires `--auto`) |

## Usage Examples

```bash
# Manual (unchanged)
/issue-task ONC-5

# Auto-loop tasks only (manual start/finish)
/issue-task --auto

# Auto-loop + auto-finish
/issue-task --auto --finish

# Auto-loop, skip failing tasks
/issue-task --auto --on-failure=skip

# Fully autonomous with plan approval pause
/issue-start ONC-5 --auto

# Fully autonomous, no pauses
/issue-start ONC-5 --auto --no-confirm
```

## Auto-Loop Flow

When `--auto` is detected in `$ARGUMENTS`:

1. Steps 1-2 (determine issue ID, read plan) run once at the start
2. Steps 3-6 become a loop:
   - `bd list --status open` → no tasks? exit loop
   - Claim next unblocked task → `bd update <id> --claim`
   - Read task details → `bd show <id>`
   - Implement task (read files, make changes, run tests, fix failures)
   - Evaluate outcome:
     - Tests pass → proceed to commit
     - Tests fail / stuck → `--on-failure=stop` exits, `--on-failure=skip` logs and continues
   - Stage + commit (inlined commit logic, Conventional Commits format)
   - Close Beads task → `bd close <id>`
   - Show progress summary
   - Loop back

After loop exit:
- If `--finish` → run `/issue-finish` logic (push, PR, Linear update)
- Show final summary

## Progress Summary (after each task)

```
--- Task 3/7 Complete ───────────────────
  Task:     onc-12 — Add patient export endpoint
  Commit:   feat(ONC-5): add patient export endpoint
  Tests:    4 passed, 0 failed
  Changed:  3 files
  Remaining: 4 tasks
─────────────────────────────────────────
```

## Final Summary (when loop ends)

```
--- Auto-Loop Complete ─────────────────
  Issue:     ONC-5
  Completed: 6/7 tasks
  Skipped:   1 task (onc-15 — test failures)
  Commits:   6

  Completed tasks:
    completed onc-10 — feat(ONC-5): add patient model
    completed onc-11 — feat(ONC-5): add export service
    ...

  Skipped tasks:
    failed onc-15 — Integration test setup (tests failed)

  Next: /issue-finish (or already done if --finish was used)
─────────────────────────────────────────
```

## Implementation Approach

The loop logic lives in the `issue-task.md` command prompt itself, not as a script. When `--auto` is detected:

- The commit logic from `commit.md` is inlined into the auto-loop instructions (stage files safely, conventional commit format, close Beads task)
- No cross-command invocation needed — keeps the flow self-contained
- The same safety rules apply (no `git add -A`, no sensitive files, HEREDOC commit messages)

For `/issue-start --auto`, the command instructions are extended to chain into the auto-loop flow after plan creation/approval, embedding the same loop logic or instructing continuation with the auto-loop behavior.

## Files to Modify

1. `plugins/issue-lifecycle/commands/issue-task.md` — Add flag parsing, loop logic, inlined commit logic, progress/final summaries
2. `plugins/issue-lifecycle/commands/issue-start.md` — Add `--auto` and `--no-confirm` flags, chain into auto-loop after planning
