---
description: Scaffold a new skill plugin (from a description or existing folder) and open a PR
argument-hint: "<skill-name> [--from <path-to-existing-skill-folder>]"
allowed-tools: Bash(git:*), Bash(gh:*), Bash(bin/sync-versions.sh:*), Bash(python3:*)
---

## Context

Dynamic values injected at runtime:

- Repo root check: !`git rev-parse --show-toplevel 2>/dev/null || echo "NOT_A_GIT_REPO"`
- Origin: !`git remote get-url origin 2>/dev/null || echo "NO_ORIGIN"`
- gh auth: !`gh auth status 2>&1 | head -1 || echo "GH_UNAUTHENTICATED"`
- Existing plugins: !`ls plugins 2>/dev/null`

## Instructions

You are scaffolding a new skill plugin in the `slickage/claude-plugins` marketplace and opening a PR for it. Follow these steps precisely. Use `$ARGUMENTS` as the raw user input.

### Step 1: Parse arguments

From `$ARGUMENTS`, extract:

- `<skill-name>` — the first positional token. It MUST be lowercase-kebab-case (e.g. `my-cool-skill`).
- `--from <path>` — optional. If present, extract the path that follows it.

This determines the mode:

- **describe mode** — no `--from` flag. You will build the skill content from a short interview.
- **import mode** — `--from <path>` given. You will copy an existing local skill folder.

### Step 2: Preflight guards

Refuse cleanly and stop if ANY condition below holds. Do NOT half-write files; leave the working tree clean. Output a single-line reason and stop.

- The origin (from Context) does not point at `slickage/claude-plugins`.
- `gh` is not authenticated (the Context gh-auth line shows `GH_UNAUTHENTICATED` or any failure message).
- `<skill-name>` is empty, is not lowercase-kebab-case, or `plugins/<skill-name>/` already exists.
- (import mode only) the `--from` path is not a directory, or it contains no `SKILL.md`.

### Step 3: Gather skill content

**describe mode:**

Use the `AskUserQuestion` tool to ask the author for exactly two inputs:

1. A one-line "what it does".
2. A one-line "when to use".

Keep it to these two inputs — no multi-turn interview. From the answers, build a minimal conformant `SKILL.md` with YAML frontmatter (`name` + `description`) plus a short body. Use `<skill-name>` as `name`, and fold "what it does" and "when to use" into the `description`.

**import mode:**

Copy the contents of the `--from` folder into the new layout (described in Step 4). The folder must already contain a `SKILL.md`; reuse it as-is.

### Step 4: Scaffold the layout

Create this exact structure:

```
plugins/<skill-name>/.claude-plugin/plugin.json
plugins/<skill-name>/skills/<skill-name>/SKILL.md
plugins/<skill-name>/README.md
```

- `plugin.json` — fields: `name` (`<skill-name>`), `version` (`"0.1.0"`), `description`, `author` (`{ "name": "slickage" }`), `homepage` and `repository` (both `https://github.com/slickage/claude-plugins`), `license` (`MIT`), and a `keywords` array.
- `skills/<skill-name>/SKILL.md` — the content from Step 3.
- `README.md` — a one-paragraph summary of the skill.

`plugin.json` is the version source of truth — set it to `0.1.0`. NEVER hand-edit versions in `marketplace.json`.

### Step 5: Sync, branch, commit, push, PR

Run:

```bash
bin/sync-versions.sh
git checkout -b feat/skill-<skill-name>
git add plugins/<skill-name> .claude-plugin/marketplace.json README.md
git commit -m "feat(<skill-name>): add skill via /slickage-skill:new"
git push -u origin feat/skill-<skill-name>
gh pr create --fill --title "feat(<skill-name>): add skill" --body "Scaffolded via /slickage-skill:new. Review = quality gate."
```

If `bin/sync-versions.sh` fails, abort and report; leave the tree clean.

### Step 6: Present results

```
--- Skill scaffolded ---
  Skill:   <skill-name>
  Mode:    describe | import
  Branch:  feat/skill-<skill-name>
  PR:      <url>
------------------------
```
