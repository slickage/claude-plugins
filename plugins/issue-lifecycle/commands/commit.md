---
description: Semantic commit (Conventional Commits format) with Beads task completion
allowed-tools: Bash(git add:*), Bash(git status:*), Bash(git commit:*), Bash(git diff:*), Bash(bd close:*), Bash(bd update:*), Bash(bd list:*), Bash(bd show:*)
---

## Context

- Current git status: !`git status`
- Staged and unstaged diff: !`git diff HEAD`
- Current branch: !`git branch --show-current`
- Recent commits: !`git log --oneline -10`
- In-progress Beads tasks: !`bd list --status in_progress --limit 5 2>/dev/null || echo "NO_BEADS_TASKS"`

## Instructions

You are creating a Conventional Commits formatted commit and closing the active Beads task. Execute these 6 steps with no extra commentary.

### Step 1: Parse issue ID from branch

Extract the issue ID from the current branch name. The branch format is `<prefix>/<issue-id>-<slug>` (e.g., `feat/onc-5-add-audit-trail` -> `ONC-5`).

- Parse the issue ID and convert to UPPERCASE (e.g., `onc-5` -> `ONC-5`).
- If the branch does not match this format (e.g., `main`, `master`, `develop`, or any branch without a `/` and issue ID pattern), proceed without a scope. Set issue ID to empty.

### Step 2: Determine commit type

Analyze the staged and unstaged diff from context above. Choose exactly ONE type:

- `feat` — New feature or user-facing functionality
- `fix` — Bug fix
- `chore` — Config, dependencies, build, tooling
- `docs` — Documentation only
- `refactor` — Code restructuring, no behavior change
- `test` — Adding or updating tests only
- `perf` — Performance improvement
- `style` — Formatting, whitespace only

Pick the type that best describes the PRIMARY purpose of the changes. When in doubt between `feat` and `refactor`, prefer `feat` if there is any user-facing behavior change.

### Step 3: Draft commit message

Build the commit message in this exact format:

- **With issue ID:** `type(ISSUE-ID): concise description`
- **Without issue ID:** `type: concise description`

Rules:
- Type is lowercase
- Issue ID is UPPERCASE inside parentheses
- Description is lowercase, imperative mood ("add", "fix", "update" — not "added", "fixes", "updated")
- No period at end
- Total message under 72 characters
- Be specific about what changed, not vague (e.g., "add patient export button" not "update UI")

### Step 4: Stage and commit

First, stage all relevant changed files. NEVER stage files matching these patterns: `.env*`, `credentials*`, `*secret*`, `*.pem`, `*.key`, `config/master.key`, `config/credentials.yml.enc`.

Stage files individually or by directory — do NOT use `git add -A` or `git add .` unless you have verified no sensitive files are present.

```bash
git add <file1> <file2> ...
```

Then commit using HEREDOC format:

```bash
git commit -m "$(cat <<'EOF'
type(ISSUE-ID): description
EOF
)"
```

### Step 5: Close the Beads task

Check the in-progress Beads tasks from context above.

- **If there is an in-progress task:** close it:
  ```bash
  bd close <task-id>
  ```
- **If context shows "NO_BEADS_TASKS" or no in-progress tasks:** skip this step.

### Step 6: Show result

After completing all steps, show this summary and nothing else:

```
--- Commit Complete ---

  Commit:   <full commit message>
  Branch:   <current branch>
  Task:     <task-id closed, or "none">

  Remaining: <count of open tasks, or run `bd list` to check>

  Next: Run /issue-task for the next task, or /issue-finish if all tasks are done.

-----------------------
```
