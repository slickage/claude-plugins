---
description: Publish a skill/hook/MCP to the team catalog — host it here if it's yours, endorse it if third-party — and add the Notion row
argument-hint: "<name> [--type skill|hook|mcp] [--from <path>] [--source <upstream-install-or-url>]"
allowed-tools: Bash(git:*), Bash(gh:*), Bash(bin/sync-versions.sh:*), Bash(python3:*), mcp__plugin_Notion_notion__notion-create-pages, mcp__plugin_Notion_notion__notion-search
---

## Context

Dynamic values injected at runtime:

- Repo root check: !`git rev-parse --show-toplevel 2>/dev/null || echo "NOT_A_GIT_REPO"`
- Origin: !`git remote get-url origin 2>/dev/null || echo "NO_ORIGIN"`
- gh auth: !`gh auth status 2>&1 | head -1 || echo "GH_UNAUTHENTICATED"`
- Existing plugins: !`ls plugins 2>/dev/null`
- git user: !`git config user.name 2>/dev/null || echo ""`

## Instructions

You add an entry to the Slickage team catalog. There is ONE entry point and it takes one of two paths automatically:

- **HOST** — the entry is **yours / locally written** and not published anywhere yet. You scaffold it into the `slickage/claude-plugins` marketplace, open a PR, and add a Notion catalog row pointing at `@slickage`.
- **ENDORSE** — the entry is **third-party** (already published elsewhere, e.g. `mattpocock`, `impeccable`). You do NOT host it; you only add a Notion catalog row pointing at its upstream install string.

Both paths end by writing a Notion row — that row is what makes the entry show up for teammates running `/slickage-catalog:sync`. Use `$ARGUMENTS` as the raw user input. The entry can be a **skill**, a **hook**, or an **mcp** server.

### Step 1: Parse arguments and determine the path

From `$ARGUMENTS`, extract:

- `<name>` — first positional token. MUST be lowercase-kebab-case (e.g. `my-cool-plugin`).
- `--type <skill|hook|mcp>` — optional, default `skill`.
- `--from <path>` — optional. A local folder to import (HOST).
- `--source <upstream>` — optional. An upstream install string or URL the entry is already published at (ENDORSE), e.g. `/plugin install foo@bar` or `https://github.com/mattpocock/foo`.

Determine the path:

1. If `--source` is given (and `--from` is not) → **ENDORSE**.
2. Else if `--from` is given → **HOST** (import mode).
3. Else (neither flag) → ask with `AskUserQuestion`: *"Is this your own entry to host in `slickage/claude-plugins`, or a third-party one to endorse?"*
   - **Host mine** → HOST (describe mode — you'll build it from a short interview).
   - **Endorse third-party** → ENDORSE (you'll ask for its upstream install string).
4. If BOTH `--from` and `--source` are given → ambiguous; ask the same question to pick the path.

Validate `--type` is exactly `skill`, `hook`, or `mcp`; stop with a one-line reason otherwise.

---

## PATH A — HOST (scaffold into the repo + PR + Notion row)

### Step A1: Preflight guards

Refuse cleanly and stop (leave the working tree clean, one-line reason) if ANY holds:

- The origin (from Context) does not point at `slickage/claude-plugins`.
- `gh` is not authenticated (Context gh-auth line shows `GH_UNAUTHENTICATED` or a failure).
- `<name>` is empty, not lowercase-kebab-case, or `plugins/<name>/` already exists.
- (import mode) the `--from` path is not a directory, or it lacks the type's key file: `skill` → `SKILL.md`, `hook` → `hooks.json`, `mcp` → `.mcp.json`.

### Step A2: Gather content (by type)

Within each type, **describe mode** runs a short interview; **import mode** copies the `--from` folder.

**Type `skill`** — describe: `AskUserQuestion` for (1) a one-line "what it does", (2) a one-line "when to use". Build a minimal conformant `SKILL.md` (YAML frontmatter `name` + `description`, short body); use `<name>` as `name` and fold both answers into `description`. Import: copy the folder; it must contain `SKILL.md`.

**Type `hook`** — describe: `AskUserQuestion` for (1) hook event — one of `PreToolUse`, `PostToolUse`, `UserPromptSubmit`, `SessionStart`, `Stop`; (2) optional matcher (e.g. `Bash`, `Write|Edit`; blank for events without matchers); (3) the shell command / one-line script body. Import: copy the folder; it must contain `hooks.json`.

**Type `mcp`** — describe: `AskUserQuestion` for (1) remote HTTPS URL OR local launch command; (2) the NAMES of any secret env vars (may be none). NEVER ask for or write secret VALUES. Import: copy the folder; it must contain `.mcp.json`.

### Step A3: Scaffold the layout

Common files for every type:

```
plugins/<name>/.claude-plugin/plugin.json
plugins/<name>/README.md
```

- `plugin.json` — `name` (`<name>`), `version` `"0.1.0"`, `description`, `author` `{ "name": "slickage" }`, `homepage` and `repository` both `https://github.com/slickage/claude-plugins`, `license` `MIT`, `keywords` array.
- `README.md` — one-paragraph summary.

Then add a section to the **root** `README.md` so `bin/sync-versions.sh` tracks the version (it warns otherwise). Under `## Available Plugins`, before that section's trailing `---`:

```markdown
### <name> (v0.1.0)

<one-line description>. Install: `/plugin install <name>@slickage`.

---
```

`plugin.json` is the version source of truth — set `0.1.0`. NEVER hand-edit versions in `marketplace.json`; let `bin/sync-versions.sh` derive them.

Type-specific files:

- **`skill`** → `plugins/<name>/skills/<name>/SKILL.md` (built in A2 or copied on import).
- **`hook`** → `plugins/<name>/hooks/hooks.json` + `plugins/<name>/scripts/<name>.sh`. Write the command/script body into the script (with `#!/usr/bin/env bash`), `chmod +x` it, and register it in `hooks.json` as a `command` hook under the chosen event/matcher, referencing `"${CLAUDE_PLUGIN_ROOT}"/scripts/<name>.sh`:

  ```json
  {
    "hooks": {
      "<EVENT>": [
        { "matcher": "<matcher or omit>", "hooks": [ { "type": "command", "command": "\"${CLAUDE_PLUGIN_ROOT}\"/scripts/<name>.sh" } ] }
      ]
    }
  }
  ```

  Omit `"matcher"` entirely for events that take none. Import: copy the existing `hooks.json` as-is.
- **`mcp`** → `plugins/<name>/.mcp.json`. Remote: `{ "mcpServers": { "<name>": { "url": "<https url>", "headers": { "Authorization": "Bearer ${user_config.TOKEN}" } } } }`. Local: `{ "mcpServers": { "<name>": { "command": "<cmd>", "args": [] } } }`. For each secret env var named in A2, add a `userConfig` entry to `plugin.json` marked `sensitive` and reference it only as `${user_config.KEY}`. NEVER write secret VALUES into the repo. Import: copy the existing `.mcp.json` as-is.

### Step A4: Sync, branch, commit, push, PR

```bash
bin/sync-versions.sh
git checkout -b feat/plugin-<name>
git add plugins/<name> .claude-plugin/marketplace.json README.md
git commit -m "feat(<name>): add plugin via /slickage-catalog:publish"
git push -u origin feat/plugin-<name>
gh pr create --fill --title "feat(<name>): add plugin" --body "Published via /slickage-catalog:publish. Review = quality gate."
```

If `bin/sync-versions.sh` fails, abort and report; leave the tree clean.

For the Notion row (Step 3 below): the `Install` value is `/plugin install <name>@slickage` and `Source` is `https://github.com/slickage/claude-plugins`. (It becomes installable once the PR merges.)

Then continue to **Step 3 (shared)**.

---

## PATH B — ENDORSE (Notion row only, no hosting)

### Step B1: Preflight guards

Stop with a one-line reason if:

- The Notion tools (`mcp__plugin_Notion_notion__notion-create-pages` / `notion-search`) are unavailable in this session — the catalog row is the only output, so this path can't proceed. Tell the user to connect the Notion MCP.

(No repo / `gh` / origin checks — endorsing does not touch this repo.)

### Step B2: Gather the upstream entry

You need enough to write a useful catalog row. Use `--source` if given; otherwise ask. Gather:

- `<name>` (already parsed).
- **Install string** — REQUIRED. The exact command a teammate runs to install it (e.g. `/plugin install foo@bar`, or `/plugin marketplace add owner/repo` → `/plugin install foo@foo`). If the entry has NO install string because it is unpublished / local-only, STOP and tell the user: *"This isn't published anywhere installable — run `/slickage-catalog:publish <name> --from <folder>` to host it here instead."*
- **Source URL** — where it lives upstream (repo / marketplace / docs URL).
- **What it does** — one line.
- **When to use** (skill/mcp) — one line.
- For `hook`: a one-line **Trigger** description and the hook **Type** label (`Hook`, `CLI Tool`, or `MCP`).

For the Notion row (Step 3 below): `Install` = the upstream install string; `Source` = the upstream URL.

Then continue to **Step 3 (shared)**.

---

## Step 3 (shared): Add the Notion catalog row

Write ONE row into the data source that matches `--type`. First, **dedupe**: call `mcp__plugin_Notion_notion__notion-search` against the target `data_source_url` with the entry name as the query; if a row with the same title already exists, ask the user whether to skip (leave the existing row) or add anyway.

Create the row with `mcp__plugin_Notion_notion__notion-create-pages`, parent `{ "data_source_id": "<collection-id>" }`, properties per the table's schema:

- **skill** → `collection://4c032e9f-e7b1-4181-87a7-0ade9d821351`
  - `Skill` (title) = `<name>`, `Install`, `Source`, `What it does`, `When to use`, `Used by` = the Context git-user name (or blank), `Category` = one of `Design/UX`, `Code Quality`, `Workflow`, `Dev Tooling`, `Communication` (pick the best fit, ask if unsure).
- **mcp** → `collection://6102c793-cb36-4344-b911-d075e419b5c7`
  - `Integration` (title) = `<name>`, `Install`, `Source`, `What it does`, `When to use`, `Used by`.
- **hook** → `collection://c7feed0e-d455-45c0-ac5b-6e9e1d4e6c0f`
  - `Name` (title) = `<name>`, `Type` (`Hook` / `CLI Tool` / `MCP`), `Install / Config` = the install string, `Source`, `Trigger`, `What it does`, `Used by`.

Use the `Install` / `Source` values set by whichever path ran (HOST → `@slickage` + repo URL; ENDORSE → upstream string + upstream URL).

**HOST resilience:** if the Notion tools are unavailable on the HOST path, do NOT fail — the PR already exists. Warn, and print the row's field values so the user can add it to Notion by hand.

### Step 4: Present results

```
--- Published to catalog ---
  Name:    <name>
  Type:    skill | hook | mcp
  Path:    HOST (PR <url>) | ENDORSE
  Notion:  row added to <Skills|MCP Integrations|Hooks & Tools>  (or "skipped — already exists" / "manual — Notion unavailable")
----------------------------
```
