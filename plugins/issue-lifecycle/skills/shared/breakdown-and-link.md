# Breakdown and link (shared core)

The logic both skills use to turn a main issue into a linked BEADS + tracker structure. The mapping is strictly **1:1** — one child BEADS task per sub-issue. The **parent BEADS task** pairs with the existing main issue (no extra tracker entity) and is blocked by every child, so it closes last.

```
TRACKER                          BEADS
main issue (MAIN-1)   <--1:1-->  parent task (blocked by all children)
  sub-issue 1         <--1:1-->    child task 1
  sub-issue 2         <--1:1-->    child task 2
  sub-issue 3         <--1:1-->    child task 3
```

## Linking convention (the resume anchor)

The link is what makes the whole run resumable from observable artifacts — there is no separate state file.

- **BEADS → tracker:** each task's `--external-ref` holds the tracker sub-issue id/key. The parent task's `--external-ref` holds the main issue id/key.
- **Tracker → BEADS:** each sub-issue's description ends with a marker line `beads: <task-id>`.
- **Label:** every task (parent + children) carries `-l "<ISSUE-ID>"` for filtering.

A child task **has** a sub-issue iff its `external-ref` is set. This is the guard for "sub-issue already created".

## Initialize BEADS (if needed)

If `bd status` shows the DB is not initialized:

```bash
bd init --prefix <project-directory-name> --skip-hooks
```

Use a short 3–4 char abbreviation of the project directory name (e.g. `oncuria` → `onc`).

## Procedure

Run these guarded steps. Each is idempotent — skip when its artifact already exists.

### 1. Determine the child set

Decide the child tasks one of three ways, in priority order:

1. **Tasks already exist** — `bd list -l "<ISSUE-ID>" --limit 50` returns children → skip breakdown entirely (already scaffolded). Reconcile only missing sub-issues (step 4).
2. **Adopt** — no BEADS tasks, but `listSubIssues(<ISSUE-ID>)` returns existing children → create one child task per existing sub-issue (1:1), respecting the human's breakdown. Set each task's `external-ref` to its sub-issue id.
3. **Invent** — no BEADS tasks and no existing sub-issues → derive an actionable breakdown from the plan/requirements (specific tasks with exact file paths), then create matching sub-issues in step 4.

### 2. Create the parent task

If no parent task exists for `<ISSUE-ID>` (a labeled task whose `external-ref` = the main issue id):

```bash
bd create "<Issue title>" -d "Overarching task for <ISSUE-ID>. <one-line goal>" -p 1 --external-ref "<MAIN-ISSUE-ID>" -l "<ISSUE-ID>"
```

### 3. Create child tasks

For each child in the set that does not yet exist:

```bash
bd create "Task title" -d "Detailed description including exact file paths" -p 2 --external-ref "<sub-issue-id-or-PENDING>" -l "<ISSUE-ID>"
```

For the **invent** path, sub-issues do not exist yet — create the task with `--external-ref "PENDING-<n>"`, then fill the real id in step 4.

### 4. Create + link sub-issues (guarded)

For each child task whose `external-ref` is `PENDING-*` (no real sub-issue yet):

1. `createSubIssue(parentId=<MAIN-ISSUE-ID>, title=<task title>, description=<task description> + "\n\nbeads: <task-id>")`.
2. Update the task's external-ref to the returned sub-issue id: `bd update <task-id> --external-ref "<sub-issue-id>"`.

For **adopt**, the sub-issue already exists — only ensure its description carries the `beads: <task-id>` marker (`comment` or update if the provider supports description edits; otherwise post a linking comment).

### 5. Set dependencies

Block the parent on every child so it closes last:

```bash
bd dep add <parent-task-id> <child-task-id>   # parent depends on each child
```

Add sequential child→child dependencies only where the plan requires ordering.

## Result

After this core runs, the **scaffold** exists: main issue + 1:1 sub-issues + parent/child BEADS tasks, fully linked. Re-running is safe — every step's guard skips completed work.
