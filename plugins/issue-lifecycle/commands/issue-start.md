---
description: Start working on a Linear issue - fetches issue, creates plan, Beads tasks, and feature branch
argument-hint: Linear issue ID (e.g., ONC-5)
---

## Context

- Current branch: !`git branch --show-current`
- Beads status: !`bd status 2>/dev/null || echo "BEADS_NOT_INITIALIZED"`
- Existing plans: !`ls docs/plans/ 2>/dev/null || echo "NO_PLANS_DIR"`
- Project directory name: !`basename $(pwd)`

## Instructions

You are starting work on Linear issue: **$ARGUMENTS**

Follow these steps precisely:

### Step 1: Fetch the Linear issue

Use the `mcp__plugin_linear_linear__get_issue` tool with id: `$ARGUMENTS`.
Save the response — you'll need the title, description, labels, URL, and UUID (id field).

### Step 2: Research the codebase

Based on the issue description, use Glob, Grep, and Read to search the codebase for relevant files (models, controllers, views, services, tests). Understand the existing architecture that relates to this issue. Be thorough — this context feeds the plan.

### Step 3: Write the plan document

Create the directory `docs/plans/` if it doesn't exist, then write the plan to `docs/plans/<ISSUE-ID>.md` (e.g., `docs/plans/ONC-5.md`).

Plan format:

```
# <Issue Title> — <ISSUE-ID>

> **Linear:** <issue URL>
> **Branch:** <branch name (determined in step 7)>
> **Date:** <today's date>
> **Status:** In Progress

## Issue Description

<Copy from Linear issue description>

## Codebase Context

<What you found during research — relevant files, existing patterns, architecture notes>

## Implementation Approach

<High-level approach in 2-3 sentences>

## Tasks

1. <Specific, actionable task with exact file paths>
2. <Next task>
3. ...

## Testing Strategy

<How to verify each task — specific test files, manual testing steps>

## Notes

<Gotchas, risks, decisions, alternative approaches considered>
```

### Step 4: Present the plan for review

Show the user a summary of the plan:

```
--- Plan Created for <ISSUE-ID> ---

  Issue:    <ISSUE-ID> — <Title>
  Plan:     docs/plans/<ISSUE-ID>.md

  Tasks:
    1. <task title>
    2. <task title>
    ...

  Please review the plan at docs/plans/<ISSUE-ID>.md.
  You can discuss changes with me before proceeding.

------------------------------------
```

Then use `AskUserQuestion` to ask the user:
- Question: "Does the plan look good? Ready to create Beads tasks and start the branch?"
- Options: "Approve plan" (proceed to step 5), "I have changes" (wait for user to discuss edits)

**If the user selects "I have changes":** Stop and wait. Let them discuss modifications. After they are satisfied and confirm, resume from Step 5.

**If the user selects "Approve plan":** Continue to Step 5.

### Step 5: Initialize Beads (if needed)

If the context above shows "BEADS_NOT_INITIALIZED", initialize Beads using the project directory name as the prefix:

```bash
bd init --prefix <project-directory-name> --skip-hooks
```

For example, if the project directory is `oncuria`, run `bd init --prefix onc --skip-hooks`. Use a short, recognizable abbreviation of the project name (3-4 characters).

### Step 6: Create Beads tasks

For each task in the plan, create a Beads task:
```bash
bd create "Task title" -d "Detailed description including file paths" -p 2 --external-ref "<ISSUE-ID>-<short-suffix>" -l "<ISSUE-ID>"
```

The `--external-ref` must be unique per task. Use the pattern `<ISSUE-ID>-<short-suffix>` (e.g., `ONC-5-gem`, `ONC-5-models`, `ONC-5-tests`).

After creating all tasks, set up dependencies between sequential tasks:
```bash
bd dep add <child-id> <parent-id>
```

Where `<child-id>` depends on `<parent-id>` completing first.

### Step 7: Create and checkout the feature branch

Determine the branch prefix from the Linear issue labels:
- "Feature" → `feat/`
- "Bug" → `fix/`
- "Improvement" or "Chore" → `chore/`
- "Documentation" → `docs/`
- Default (no matching label) → `feat/`

Branch format: `<prefix>/<issue-id-lowercase>-<slug-from-linear-title>`

Example: For ONC-5 "Add Audit Trail" with label "Feature" → `feat/onc-5-add-audit-trail`

Create the branch:
```bash
git checkout -b <branch-name>
```

### Step 8: Update Linear status to In Progress

Use `mcp__plugin_linear_linear__update_issue` with:
- id: <the issue's UUID from step 1>
- state: "In Progress"

### Step 9: Present summary

Show the user:

```
--- Issue Lifecycle Started ---

  Issue:    <ISSUE-ID> — <Title>
  Plan:     docs/plans/<ISSUE-ID>.md
  Branch:   <branch-name>
  Linear:   In Progress
  Tasks:    <count> created

  Beads Tasks:
    <id>  <title>
    <id>  <title>
    ...

  Next: Run /issue-task to start working on the first task.

--------------------------------
```
