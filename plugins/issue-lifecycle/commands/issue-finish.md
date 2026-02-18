---
description: Finish a Linear issue - push branch, create PR, update Linear to In Review, post completion comment
argument-hint: Optional Linear issue ID (infers from branch if omitted)
---

## Context

- Current branch: !`git branch --show-current`
- Git status: !`git status`
- Commits on branch: !`git log main..HEAD --oneline 2>/dev/null || git log master..HEAD --oneline 2>/dev/null || echo "COULD_NOT_DETERMINE_BASE"`
- All Beads tasks: !`bd list --all --limit 50 2>/dev/null || echo "NO_BEADS_DB"`
- Open Beads tasks: !`bd list --status open --limit 10 2>/dev/null || echo "NONE_OPEN"`
- Beads prefix: !`bd config get prefix 2>/dev/null || basename $(pwd) | cut -c1-3`

## Instructions

You are finishing work on a Linear issue — pushing the branch, creating a PR, updating Linear status, and posting a completion comment. Follow these 8 steps precisely.

### Step 1: Determine the issue ID

Determine which Linear issue to finish:

- **If `$ARGUMENTS` is provided and not empty:** use it as the issue ID (convert to UPPERCASE).
- **If `$ARGUMENTS` is empty or not provided:** parse the issue ID from the current branch name. The branch format is `<prefix>/<issue-id>-<slug>` (e.g., `feat/onc-5-add-audit-trail` -> `ONC-5`). Extract the issue ID portion and convert to UPPERCASE.
- **Mismatch check:** if `$ARGUMENTS` was provided AND a branch-based issue ID can also be parsed, compare them. If they differ, **warn the user** clearly:
  ```
  WARNING: Argument issue ID (<arg>) does not match branch issue ID (<branch>).
  Branch: <current branch>
  ```
  Ask the user to confirm which issue they want to finish before proceeding. Do NOT continue until the user confirms.

If neither `$ARGUMENTS` nor the branch yields a valid issue ID, tell the user and stop.

### Step 2: Pre-flight checks

Perform these checks before proceeding:

#### 2a. Uncommitted changes

Check the git status from context above. If there are ANY uncommitted changes (staged or unstaged, untracked files that appear to be project files), **STOP** and tell the user:

```
STOP: You have uncommitted changes. Please run /commit first to commit your work before finishing the issue.
```

Do NOT proceed past this step if uncommitted changes exist.

#### 2b. Incomplete Beads tasks

Check the open Beads tasks from context above. If there are any open or in-progress tasks remaining (and context does not show "NONE_OPEN" or "NO_BEADS_DB"), **warn the user** with the list of incomplete tasks:

```
WARNING: The following Beads tasks are still incomplete:

  <task-id>  <task-title>  (<status>)
  <task-id>  <task-title>  (<status>)
  ...

Do you want to proceed with finishing the issue anyway?
```

Use `AskUserQuestion` to ask the user to confirm before continuing. If they say no, stop. If they say yes, proceed.

### Step 3: Fetch the Linear issue

Use `mcp__plugin_linear_linear__get_issue` with id: `<ISSUE-ID>` (the ID determined in Step 1).

Save the response — you will need the **title**, **description**, **URL**, and **UUID** (the `id` field) for subsequent steps.

### Step 4: Push the branch

Push the current branch to origin:

```bash
git push -u origin <branch-name>
```

Where `<branch-name>` is the current branch from context above.

### Step 5: Create the Pull Request

Create a pull request using `gh pr create`. Build the PR with:

- **Title:** The Linear issue title (from Step 3)
- **Body** must include all of the following sections:
  1. `Closes <ISSUE-ID>` — this line triggers the Linear GitHub integration for auto-linking
  2. A summary section with 2-3 bullet points derived from the commits on the branch and the Beads tasks
  3. A link to the Linear issue URL
  4. A completed tasks checklist from Beads (all tasks listed, checked off if done)
  5. A test plan section

Use HEREDOC format for the body:

```bash
gh pr create --title "<Linear issue title>" --body "$(cat <<'EOF'
Closes <ISSUE-ID>

## Summary

- <bullet 1 — derived from commits and tasks>
- <bullet 2>
- <bullet 3 if applicable>

## Linear Issue

<Linear issue URL>

## Completed Tasks

- [x] <task-1-title>
- [x] <task-2-title>
- [ ] <incomplete-task-title (if any were skipped)>

## Test Plan

- <testing step 1>
- <testing step 2>
EOF
)"
```

Capture the PR URL from the output of `gh pr create`.

### Step 6: Update Linear status to In Review

Use `mcp__plugin_linear_linear__update_issue` with:
- id: `<the issue's UUID from Step 3>`
- state: "In Review"

### Step 7: Post completion comment on Linear

Use `mcp__plugin_linear_linear__create_comment` to post a comment on the issue. Use the Beads prefix from the context above. The comment body must follow this format:

```
:white_check_mark: **Completed**

All implementation tasks have been completed:

### Completed Tasks (beads: <beads-prefix>)
- :white_check_mark: <task-1-title>
- :white_check_mark: <task-2-title>
- ...

### Implementation Summary
- <bullet 1 — brief description of what was implemented>
- <bullet 2>

PR: <PR-URL>
```

Use the actual unicode/emoji characters for checkmarks. List all Beads tasks — mark completed ones with a checkmark and incomplete ones (if any were skipped with user confirmation) with a warning indicator.

### Step 8: Show final summary

Present the following summary to the user:

```
--- Issue Finished ---

  Issue:    <ISSUE-ID> — <Title>
  PR:       <PR-URL>
  Linear:   In Review
  Tasks:    <completed count>/<total count> completed

  The Linear issue will automatically move to Done
  when the PR is merged (via GitHub integration).

  Next: Share the PR link for code review.

----------------------
```
