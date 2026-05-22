# Commit and close (shared core)

Per-task commit + BEADS close + sub-issue sync. Folded in from the old `/commit` command; called by the Lifecycle skill once a task's implementation passes its tests.

## 1. Parse issue ID for scope

Use the run's issue ID (already UPPERCASE). If working outside an issue branch, omit the scope.

## 2. Determine commit type

Analyze `git diff HEAD`. Choose exactly ONE:

- `feat` — new feature or user-facing functionality
- `fix` — bug fix
- `chore` — config, dependencies, build, tooling
- `docs` — documentation only
- `refactor` — code restructuring, no behavior change
- `test` — adding or updating tests only
- `perf` — performance improvement
- `style` — formatting, whitespace only

When in doubt between `feat` and `refactor`, prefer `feat` if there is any user-facing behavior change.

## 3. Draft commit message

- With issue ID: `type(ISSUE-ID): concise description`
- Without issue ID: `type: concise description`

Rules: lowercase type, UPPERCASE issue ID in parentheses, lowercase imperative description ("add", "fix", "update"), no trailing period, under 72 chars, specific not vague.

## 4. Stage and commit

Never stage: `.env*`, `credentials*`, `*secret*`, `*.pem`, `*.key`, `config/master.key`, `config/credentials.yml.enc`. Stage files individually or by directory — **never** `git add -A` or `git add .`.

```bash
git add <file1> <file2> ...
git commit -m "$(cat <<'EOF'
type(ISSUE-ID): description
EOF
)"
```

## 5. Close the task and sync its sub-issue

```bash
bd close <task-id>
```

Then push the sub-issue to Done via the provider (the task's `external-ref` holds the sub-issue id):

- `updateState(<sub-issue-id>, "done")`

This is the one-way push: BEADS is the source of truth, the tracker mirrors it. (See ADR `0001-beads-source-of-truth-one-way-sync.md`.)

## 6. Parent rollup

After closing a child, if `bd list -l "<ISSUE-ID>" --status open` shows no remaining open children, the parent task is unblocked. Closing the parent (done at finish) corresponds to the main issue moving to In Review.
