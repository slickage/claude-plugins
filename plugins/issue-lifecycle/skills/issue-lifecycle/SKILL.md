---
name: Issue Lifecycle
description: This skill should be used when the user asks to "start an issue", "work on ONC-5", "implement this Linear/Jira issue", "run the issue lifecycle", "take this issue to a PR", or names a tracker issue ID to build. Drives an existing Linear or Jira issue autonomously from breakdown through implementation to an open PR, with BEADS task tracking and linked sub-issues.
version: 3.0.0
---

# Issue Lifecycle

Drive an existing tracker issue (Linear or Jira) from breakdown to an open PR in one resumable, autonomous pass. Break the issue into actionable BEADS tasks, create one linked sub-issue per task plus a parent BEADS task for the overarching issue, implement every task with a commit each, then open the PR and move the issue to In Review.

This skill **encompasses the full lifecycle** — there are no separate start/task/finish/commit steps to run. Invoke it once with an issue ID; re-invoke to resume.

## Operating principles

- **Resumable single pass.** Every step is guarded by an observable artifact (branch, `bd list`, task `external-ref`, `gh pr`). Re-invoking resumes where it left off — no bespoke state file.
- **Zero mid-run confirmation.** Run autonomously. The review point is the resumability itself: stop after the scaffold, inspect, re-invoke to continue.
- **BEADS is the source of truth.** State flows one way — BEADS → tracker. Never read tracker state back mid-run except the initial fetch. See ADR `0001`.
- **Stop and hold on failure.** If a task's tests cannot be made to pass, halt the loop, keep the changes, leave the task in-progress, report. Do not skip or revert.

## Shared references — read before acting

- **`../shared/config.md`** — resolve the active provider from `.issue-lifecycle.json`.
- **`../shared/providers.md`** — the provider contract; how Linear and Jira implement it.
- **`../shared/breakdown-and-link.md`** — the breakdown + sub-issue + parent-task core (incl. **adopt**).
- **`../shared/commit-and-close.md`** — per-task commit, `bd close`, sub-issue → Done.

## Procedure

### 1. Resolve provider

Read `.issue-lifecycle.json` per `config.md`. If the configured provider's MCP is not connected, stop with a clear message.

### 2. Determine the issue ID

Use the argument if given (UPPERCASE). Otherwise parse the current branch `<prefix>/<issue-id>-<slug>` (e.g. `feat/onc-5-... → ONC-5`). If an argument ID and branch ID both exist and differ, warn and ask which to use before continuing. If neither yields an ID, stop.

### 3. Fetch the issue

`getIssue(<ISSUE-ID>)`. Save title, description, labels/type, URL, native id/key, and existing children.

### 4. Research the codebase

Use Glob/Grep/Read to find relevant files (models, controllers, services, tests). If the project has skills in `.claude/skills/`, reference them. This context feeds the breakdown.

### 5. Ensure the feature branch (guarded)

Prefix from the issue label/type: Feature/Story → `feat/`, Bug → `fix/`, Improvement/Chore → `chore/`, Documentation → `docs/`, default `feat/`. Branch: `<prefix>/<issue-id-lowercase>-<slug>`. Checkout if it exists locally or on remote; otherwise `git checkout -b`.

### 6. Ensure the plan doc (guarded)

If `docs/plans/<ISSUE-ID>.md` is missing, write it (human-readable reference only — not resume truth): title, Linear/Jira link, branch, date, Issue Description, Codebase Context, Implementation Approach, Tasks, Testing Strategy, Notes. If it exists, keep it.

### 7. Ensure the breakdown + scaffold (guarded)

Run `../shared/breakdown-and-link.md`: init BEADS if needed, determine the child set (existing → skip, else **adopt** existing sub-issues, else **invent**), create the parent task, create child tasks, create + link 1:1 sub-issues, set dependencies. Idempotent — skips anything already present.

### 8. Move the issue to In Progress

`updateState(<ISSUE-ID>, "inProgress")`.

### 9. Implementation loop

Repeat until no open child tasks remain:

1. **Claim** the first open, unblocked child task: `bd update <task-id> --claim`. Push its sub-issue to In Progress: `updateState(<sub-issue-id>, "inProgress")`. If a task is already in-progress, continue it. If all remaining are blocked, exit the loop.
2. **Read** task details: `bd show <task-id>`; cross-reference the plan.
3. **Implement** — read source first, follow project patterns, make focused changes, run the related tests (not the whole suite), fix failures.
4. **Evaluate:**
   - Tests pass → run `../shared/commit-and-close.md` (commit, `bd close`, sub-issue → Done).
   - Tests cannot pass after reasonable effort → **stop and hold**: keep changes, leave the task in-progress and its sub-issue In Progress, report `stopped at <task-id>: <reason>`, exit. Re-invoking resumes here.
5. Show a one-line progress summary (task N/T, commit, tests, remaining), then loop.

### 10. Finish (guarded)

When all child tasks are closed:

1. Pre-flight: if `git status` shows uncommitted changes, commit them via `commit-and-close.md` first.
2. Close the parent task: `bd close <parent-task-id>`.
3. Push the branch: `git push -u origin <branch-name>`.
4. Open the PR (skip if `gh pr list --head <branch>` already shows one). Base = repo default or `--base`. Body includes `Closes <ISSUE-ID>`, a Summary, the issue URL, a Completed Tasks checklist, and a Test Plan (HEROC format).
5. `updateState(<ISSUE-ID>, "inReview")`.
6. Post a completion comment via `comment(<ISSUE-ID>, ...)` listing completed tasks (with the BEADS prefix) and an implementation summary + PR URL.

### 11. Final summary

Show issue, PR URL, tracker state (In Review), and completed/total task count. Note that the issue moves to Done when the PR merges (tracker GitHub integration).
