---
description: Finish a Linear issue - push branch, create PR, update Linear to In Review, post completion comment
argument-hint: "[ISSUE-ID] [--base <branch>]"
---

## Context

- Current branch: !`git branch --show-current`
- Git status: !`git status`
- Default branch: !`gh repo view --json defaultBranchRef --jq '.defaultBranchRef.name' 2>/dev/null || echo "main"`
- Commits on branch: !`git log $(gh repo view --json defaultBranchRef --jq '.defaultBranchRef.name' 2>/dev/null || echo "main")..HEAD --oneline 2>/dev/null || echo "COULD_NOT_DETERMINE_BASE"`
- All Beads tasks: !`bd list --all --limit 50 2>/dev/null || echo "NO_BEADS_DB"`
- Open Beads tasks: !`bd list --status open --limit 10 2>/dev/null || echo "NONE_OPEN"`
- Beads prefix: !`bd config get prefix 2>/dev/null || basename $(pwd) | cut -c1-3`

## Instructions

You are finishing work on a Linear issue — pushing the branch, creating a PR, updating Linear status, and posting a completion comment. Follow these 9 steps precisely.

### Step 1: Parse arguments and flags

Parse `$ARGUMENTS` to extract:

- **Issue ID:** Any non-flag argument (e.g., `ONC-5`). Optional — inferred from branch if omitted.
- **`--base <branch>`:** Target branch for the PR. Defaults to the repo's default branch (from context above).

Examples:
- `/issue-finish` → infer issue ID from branch, PR targets default branch
- `/issue-finish ONC-5` → explicit issue ID, PR targets default branch
- `/issue-finish --base develop` → infer issue ID, PR targets `develop`
- `/issue-finish ONC-5 --base develop` → explicit issue ID, PR targets `develop`

### Step 2: Determine the issue ID

Determine which Linear issue to finish:

- **If an issue ID was parsed from arguments:** use it (convert to UPPERCASE).
- **If no issue ID in arguments:** parse from the current branch name. The branch format is `<prefix>/<issue-id>-<slug>` (e.g., `feat/onc-5-add-audit-trail` -> `ONC-5`). Extract the issue ID portion and convert to UPPERCASE.
- **Mismatch check:** if both an argument issue ID and branch issue ID exist and they differ, **warn the user** clearly:
  ```
  WARNING: Argument issue ID (<arg>) does not match branch issue ID (<branch>).
  Branch: <current branch>
  ```
  Ask the user to confirm which issue they want to finish before proceeding. Do NOT continue until the user confirms.

If neither arguments nor the branch yields a valid issue ID, tell the user and stop.

### Step 3: Pre-flight checks

Perform these checks before proceeding:

#### 3a. Uncommitted changes

Check the git status from context above. If there are ANY uncommitted changes (staged or unstaged, untracked files that appear to be project files), **STOP** and tell the user:

```
STOP: You have uncommitted changes. Please run /commit first to commit your work before finishing the issue.
```

Do NOT proceed past this step if uncommitted changes exist.

#### 3b. Incomplete Beads tasks

Check the open Beads tasks from context above. If there are any open or in-progress tasks remaining (and context does not show "NONE_OPEN" or "NO_BEADS_DB"), **warn the user** with the list of incomplete tasks:

```
WARNING: The following Beads tasks are still incomplete:

  <task-id>  <task-title>  (<status>)
  <task-id>  <task-title>  (<status>)
  ...

Do you want to proceed with finishing the issue anyway?
```

Use `AskUserQuestion` to ask the user to confirm before continuing. If they say no, stop. If they say yes, proceed.

### Step 4: Fetch the Linear issue

Use `mcp__plugin_linear_linear__get_issue` with id: `<ISSUE-ID>` (the ID determined in Step 2).

Save the response — you will need the **title**, **description**, **URL**, and **UUID** (the `id` field) for subsequent steps.

### Step 5: Push the branch

Push the current branch to origin:

```bash
git push -u origin <branch-name>
```

Where `<branch-name>` is the current branch from context above.

### Step 6: Create the Pull Request

Create a pull request using `gh pr create`. Build the PR with:

- **Base branch:** Use the `--base` value from Step 1 (or the repo's default branch if not specified).
- **Title:** The Linear issue title (from Step 4)
- **Body** must include all of the following sections:
  1. `Closes <ISSUE-ID>` — this line triggers the Linear GitHub integration for auto-linking
  2. A summary section with 2-3 bullet points derived from the commits on the branch and the Beads tasks
  3. A link to the Linear issue URL
  4. A completed tasks checklist from Beads (all tasks listed, checked off if done)
  5. A test plan section

Use HEREDOC format for the body:

```bash
gh pr create --base <base-branch> --title "<Linear issue title>" --body "$(cat <<'EOF'
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

### Step 7: Update Linear status to In Review

Use `mcp__plugin_linear_linear__update_issue` with:
- id: `<the issue's UUID from Step 4>`
- state: "In Review"

### Step 8: Post completion comment on Linear

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

### Step 9: Show final summary

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
