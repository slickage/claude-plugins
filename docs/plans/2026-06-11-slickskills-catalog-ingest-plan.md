# SlickSkills — MVP Implementation Plan (Catalog + Ingest)

> **⚠️ Partially superseded (2026-06-11).** This plan captures the *original* MVP
> task list (catalog README + a single `/slickage-catalog:new` ingest command).
> The implementation evolved during the build — see the design doc's
> **Addendum** for the current shape: the plugin is `slickage-catalog` (renamed
> from `slickage-skill`), with two commands — **`:publish`** (host your own *or*
> endorse a third-party entry, both writing a Notion catalog row) and **`:sync`**
> (install endorsed entries from the Notion catalog). Task references to `:new`
> below are historical; the command is now `:publish`.

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Design:** `docs/plans/2026-06-11-slickskills-catalog-ingest-design.md`
**Date:** 2026-06-11
**Status:** Ready to implement

**Goal:** Stand up `slickage/claude-plugins` as the team skill catalog (Part A —
document + cross-link the endorsed set) and ship a thin `/slickage-catalog:new`
ingest command that scaffolds a conformant plugin and opens a PR (Part B).

**Architecture:** No servers, no telemetry. Catalog = README section + a pointer
from the Notion AI wiki. Ingest = one new plugin (`plugins/slickage-catalog/`) with
a single command file that scaffolds, syncs `marketplace.json` via
`bin/sync-versions.sh`, branches, and opens a PR with `gh`.

**Tech Stack:** Markdown (plugin command + READMEs); Bash 3.2 / BSD-sed +
`python3` for the existing sync script. No new runtime code.

---

## Resolved open questions

- **Ingest command depth → thin.** v1 scaffolds from a short description OR
  imports an existing local skill folder, then opens a PR. No conversational
  interview, no SKILL.md generation from chat. Enrich in v2.
- **Curation source → Notion "Skills" endorsed set.** Data source:
  `collection://4c032e9f-e7b1-4181-87a7-0ade9d821351` (parent page
  `[AI] Claude Skills, MCP, Hooks`,
  `https://app.notion.com/p/37c00840a89281119b95cd29af35d3ef`). Endorsed rows
  (9): `caveman`, `frontend-design`, `commit-commands`, `impeccable`,
  `issue-lifecycle`, `code-review`, `beads-tasks (bd)`, `Claude in Chrome`,
  `superpowers`.
- **Link, don't vendor.** 8 of 9 endorsed skills point to *external* sources
  (`claude-plugins-official`, `JuliusBrussee/caveman`, …); only `issue-lifecycle`
  is hosted here. The catalog references external ones by their native install
  string — it does NOT copy them into this repo. Locally hosted plugins stay
  `stackgen` + `issue-lifecycle` + the new `slickage-catalog`.

---

### Task 1: Add a "Catalog" section to README documenting the endorsed set

**Files:**
- Modify: `README.md` (new `## Catalog` section, placed after `## Available Plugins`)

**Step 1: Write the Catalog section**

Add a section that frames the repo as *the* team skill catalog and lists the
endorsed set as two groups:

- **Hosted here** (install `@slickage` after `/plugin marketplace add slickage/claude-plugins`):
  `stackgen`, `issue-lifecycle`.
- **Endorsed elsewhere** (install from their own marketplace) — one row per
  remaining Notion-endorsed skill with its native install string and source:
  - `superpowers` — `/plugin install superpowers@claude-plugins-official`
  - `frontend-design` — `/plugin install frontend-design@claude-plugins-official`
  - `code-review` — `/plugin install code-review@claude-plugins-official`
  - `commit-commands` — (source per Notion row)
  - `impeccable` — (source per Notion row)
  - `beads-tasks (bd)` — (source per Notion row)
  - `caveman` — `/plugin marketplace add JuliusBrussee/caveman` → `/plugin install caveman@caveman`
  - `Claude in Chrome` — extension, install from `claude.ai/chrome` (not a `/plugin`)

  Pull the exact `Install` / `Source` / `What it does` / `When to use` cells for
  `commit-commands`, `impeccable`, and `beads-tasks (bd)` from the Notion data
  source before writing (rows not yet fetched in planning).

State explicitly: external skills are referenced, not re-hosted, so upstream
stays the source of truth. Note that contributing a *new* skill to the hosted
set is done with `/slickage-catalog:new` (Task 4).

**Step 2: Run the sync script (no-op check) and commit**

```bash
bin/sync-versions.sh
git add README.md && git commit -m "docs(catalog): add team skill catalog section seeded from endorsed set"
```

`bin/sync-versions.sh` regenerates derived version tables; confirm it leaves the
new prose section untouched and produces no unexpected diff.

---

### Task 2: Point the Notion AI wiki back at the repo

**Files:**
- External: Notion page `[AI] Claude Skills, MCP, Hooks`
  (`https://app.notion.com/p/37c00840a89281119b95cd29af35d3ef`)

**Step 1: Add a callout linking to the catalog**

Near the top of the page, add a line: the canonical, installable catalog now
lives at `slickage/claude-plugins` — `/plugin marketplace add slickage/claude-plugins`,
then `/plugin install <skill>@slickage`. Keep the Notion table as the human-readable
index; the repo is the install surface.

This is the only cross-link needed for discovery (design § Part A). No code.

---

### Task 3: Scaffold the `slickage-catalog` ingest plugin skeleton

**Files:**
- Create: `plugins/slickage-catalog/.claude-plugin/plugin.json`
- Create: `plugins/slickage-catalog/commands/.gitkeep` (placeholder until Task 4)
- Create: `plugins/slickage-catalog/README.md`

**Step 1: Write `plugin.json` (version source of truth)**

Mirror the existing plugins' shape (`plugins/stackgen/.claude-plugin/plugin.json`):

```json
{
  "name": "slickage-catalog",
  "version": "0.1.0",
  "description": "Ingest path for the Slickage skill catalog: scaffold a conformant plugin from a description or an existing local skill folder and open a PR.",
  "author": { "name": "slickage" },
  "homepage": "https://github.com/slickage/claude-plugins",
  "repository": "https://github.com/slickage/claude-plugins",
  "license": "MIT",
  "keywords": ["skills", "scaffold", "ingest", "catalog", "plugin-authoring", "gh", "pr"]
}
```

**Step 2: Write a short plugin README**

One paragraph: what the plugin is (the ingest on-ramp), the single command it
provides, and its prerequisites (`gh` installed + authenticated, run from a clone
of `slickage/claude-plugins`).

**Step 3: Register in the marketplace and commit**

```bash
bin/sync-versions.sh          # adds slickage-catalog to marketplace.json + README from plugin.json
git add plugins/slickage-catalog .claude-plugin/marketplace.json README.md
git commit -m "feat(slickage-catalog): scaffold ingest plugin skeleton"
```

Verify `sync-versions.sh` picked up the new `plugin.json` and added the
`slickage-catalog` entry to `marketplace.json` automatically (never hand-edit it).

---

### Task 4: Write the `/slickage-catalog:new` command (thin v1)

**Files:**
- Create: `plugins/slickage-catalog/commands/new.md`
- Delete: `plugins/slickage-catalog/commands/.gitkeep`

Follow the command-authoring conventions in `README.md` § "Adding a New Slash
Command" (frontmatter, `## Context` with `!`-backtick injection, numbered
`### Step` instructions, literal/precise wording).

**Step 1: Frontmatter + Context**

```markdown
---
description: Scaffold a new skill plugin (from a description or existing folder) and open a PR
argument-hint: "<skill-name> [--from <path-to-existing-skill-folder>]"
allowed-tools: Bash(git:*), Bash(gh:*), Bash(bin/sync-versions.sh:*), Bash(python3:*)
---

## Context

- Repo root check: !`git rev-parse --show-toplevel 2>/dev/null || echo "NOT_A_GIT_REPO"`
- Origin: !`git remote get-url origin 2>/dev/null || echo "NO_ORIGIN"`
- gh auth: !`gh auth status 2>&1 | head -1 || echo "GH_UNAUTHENTICATED"`
- Existing plugins: !`ls plugins 2>/dev/null`
```

**Step 2: Preflight guards (refuse, don't half-write)**

Per design § Error handling, the command must refuse cleanly if any hold:

- origin does not point at `slickage/claude-plugins` (use the Context origin line)
- `gh` is not authenticated (Context gh-auth line)
- `$ARGUMENTS` skill name already exists under `plugins/` or is empty/invalid
  (lowercase-kebab only)
- (import mode) the `--from` path is not a directory containing a `SKILL.md`

Each guard prints a one-line reason and stops. Working tree must stay clean.

**Step 3: Parse arguments**

Parse `<skill-name>` and optional `--from <path>` out of `$ARGUMENTS`. Two modes:
- **describe mode** (no `--from`): ask the author for a one-line description and
  a "when to use" line via `AskUserQuestion`, then scaffold a minimal conformant
  `SKILL.md` from those two inputs (thin — no multi-turn interview).
- **import mode** (`--from <path>`): copy the existing folder's contents into the
  new plugin layout, validating it already has a `SKILL.md`.

**Step 4: Scaffold the plugin layout**

Create:
```
plugins/<skill-name>/
  .claude-plugin/plugin.json     # name, version 0.1.0, description, author slickage, repo/homepage, license
  skills/<skill-name>/SKILL.md   # from description, or copied in import mode
  README.md                      # one-paragraph summary
```
Match the field shape of an existing `plugin.json`. `plugin.json` is the version
source of truth — set `0.1.0`; do NOT touch versions in `marketplace.json`.

**Step 5: Sync, branch, commit, PR**

```bash
bin/sync-versions.sh
git checkout -b feat/skill-<skill-name>
git add plugins/<skill-name> .claude-plugin/marketplace.json README.md
git commit -m "feat(<skill-name>): add skill via /slickage-catalog:new"
git push -u origin feat/skill-<skill-name>
gh pr create --fill --title "feat(<skill-name>): add skill" \
  --body "Scaffolded via /slickage-catalog:new. Review = quality gate."
```

If `bin/sync-versions.sh` fails, abort and report — leave the tree clean
(design § Error handling).

**Step 6: Present result**

```
--- Skill scaffolded ---
  Skill:   <skill-name>
  Mode:    describe | import
  Branch:  feat/skill-<skill-name>
  PR:      <url>
------------------------
```

**Step 7: Commit the command itself**

```bash
git add plugins/slickage-catalog/commands/new.md
git rm plugins/slickage-catalog/commands/.gitkeep
git commit -m "feat(slickage-catalog): add /slickage-catalog:new ingest command"
```

---

### Task 5: Document the ingest command in the top-level README

**Files:**
- Modify: `README.md` (`## Available Plugins` → add a `slickage-catalog` entry;
  Catalog section → note "contribute a skill" points at `/slickage-catalog:new`)

**Step 1: Add the plugin entry + contribution pointer, then sync + commit**

```bash
bin/sync-versions.sh
git add README.md && git commit -m "docs(slickage-catalog): document ingest command and contribution path"
```

---

### Task 6: Manual acceptance test

Per design § Testing. Not a code change — a verification checklist run by a human.

**Step 1: Describe mode**

Run `/slickage-catalog:new test-skill-describe` end to end. Confirm it produces a
valid `plugins/test-skill-describe/` layout, a `marketplace.json` synced by the
script (not hand-edited), and an open PR. Close the PR / delete the branch after.

**Step 2: Import mode**

Run `/slickage-catalog:new test-skill-import --from <some-local-skill-folder>`.
Confirm the folder is copied into the layout and a PR opens.

**Step 3: Guard checks**

Confirm clean refusal (and clean working tree) when: not in the catalog repo,
`gh` unauthenticated, and skill name already exists.

**Step 4: Post-merge install**

After merging a real test PR, in a fresh session confirm
`/plugin install test-skill-describe@slickage` works. Then revert the test skill
(`git revert`) to keep `main` clean.

**Step 5: Lint**

`bin/sync-versions.sh` runs clean and produces no diff on a no-op.

---

## Sequencing notes

- Tasks 1–2 (catalog) and Tasks 3–5 (ingest) are independent; either can land
  first. Task 6 runs last.
- Keep each task on its own commit (repo convention). The new plugin ships at
  `0.1.0`; bump only via `plugin.json` + `bin/sync-versions.sh`.
- v2 hooks (telemetry shim, CODEOWNERS governance) are out of scope — see design
  § Future.
