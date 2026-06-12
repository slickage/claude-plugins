---
description: Scaffold a new plugin (skill, hook, or mcp — from a description or existing folder) and open a PR
argument-hint: "<name> [--type skill|hook|mcp] [--from <path>]"
allowed-tools: Bash(git:*), Bash(gh:*), Bash(bin/sync-versions.sh:*), Bash(python3:*)
---

## Context

Dynamic values injected at runtime:

- Repo root check: !`git rev-parse --show-toplevel 2>/dev/null || echo "NOT_A_GIT_REPO"`
- Origin: !`git remote get-url origin 2>/dev/null || echo "NO_ORIGIN"`
- gh auth: !`gh auth status 2>&1 | head -1 || echo "GH_UNAUTHENTICATED"`
- Existing plugins: !`ls plugins 2>/dev/null`

## Instructions

You are scaffolding a new plugin in the `slickage/claude-plugins` marketplace and opening a PR for it. The plugin can be one of three types — a **skill**, a **hook**, or an **mcp** server. Follow these steps precisely. Use `$ARGUMENTS` as the raw user input.

### Step 1: Parse arguments

From `$ARGUMENTS`, extract:

- `<name>` — the first positional token. It MUST be lowercase-kebab-case (e.g. `my-cool-plugin`).
- `--type <skill|hook|mcp>` — optional. The kind of plugin to scaffold. Allowed values are exactly `skill`, `hook`, or `mcp`. Defaults to `skill` when omitted.
- `--from <path>` — optional. If present, extract the path that follows it.

Two things are determined here, and they are independent:

The **type** (from `--type`, default `skill`) — this selects WHAT gets scaffolded in Steps 2–4.

The **mode** (from `--from`) — this selects HOW the content is gathered, and applies WITHIN each type:

- **describe mode** — no `--from` flag. You will build the plugin content from a short interview (the questions differ per type).
- **import mode** — `--from <path>` given. You will copy an existing local folder (which must contain that type's key file).

### Step 2: Preflight guards

Refuse cleanly and stop if ANY condition below holds. Do NOT half-write files; leave the working tree clean. Output a single-line reason and stop.

- The origin (from Context) does not point at `slickage/claude-plugins`.
- `gh` is not authenticated (the Context gh-auth line shows `GH_UNAUTHENTICATED` or any failure message).
- `--type` was given but is not one of `skill`, `hook`, or `mcp`.
- `<name>` is empty, is not lowercase-kebab-case, or `plugins/<name>/` already exists.
- (import mode only) the `--from` path is not a directory, or it does not contain the type's key file. The required key file depends on `--type`:
  - `skill` → `SKILL.md`
  - `hook`  → `hooks.json`
  - `mcp`   → `.mcp.json`

### Step 3: Gather content

What you gather depends on `--type`. Within each type, `describe mode` runs an interview and `import mode` copies the `--from` folder.

#### Type `skill`

**describe mode:**

Use the `AskUserQuestion` tool to ask the author for exactly two inputs:

1. A one-line "what it does".
2. A one-line "when to use".

Keep it to these two inputs — no multi-turn interview. From the answers, build a minimal conformant `SKILL.md` with YAML frontmatter (`name` + `description`) plus a short body. Use `<name>` as `name`, and fold "what it does" and "when to use" into the `description`.

**import mode:**

Copy the contents of the `--from` folder into the new layout (described in Step 4). The folder must already contain a `SKILL.md`; reuse it as-is.

#### Type `hook`

**describe mode:**

Use the `AskUserQuestion` tool to gather exactly three inputs:

1. **Hook event** — one of `PreToolUse`, `PostToolUse`, `UserPromptSubmit`, `SessionStart`, `Stop`.
2. **Matcher** — an optional matcher string (e.g. `Bash`, `Write|Edit`). Leave blank for events that do not take a matcher (such as `UserPromptSubmit`, `SessionStart`, `Stop`).
3. **Command** — the shell command to run, or a one-line script body the hook should execute.

Keep it to these three inputs — no multi-turn interview. You will fold these into `hooks/hooks.json` and `scripts/<name>.sh` in Step 4.

**import mode:**

Copy the contents of the `--from` folder into the new layout (described in Step 4). The folder must already contain a `hooks.json`; reuse it as-is.

#### Type `mcp`

**describe mode:**

Use the `AskUserQuestion` tool to gather exactly two inputs:

1. **Remote or local** — either a remote HTTPS URL the server is reached at, OR a local launch command that starts the server.
2. **Secret env var names** — the NAMES of any secret environment variables the server needs (there may be none).

NEVER ask for or write secret VALUES — only the variable names. Keep it to these two inputs — no multi-turn interview. You will fold these into `.mcp.json` (and `userConfig` entries in `plugin.json`) in Step 4.

**import mode:**

Copy the contents of the `--from` folder into the new layout (described in Step 4). The folder must already contain a `.mcp.json`; reuse it as-is.

### Step 4: Scaffold the layout

Every type creates these common files:

```
plugins/<name>/.claude-plugin/plugin.json
plugins/<name>/README.md
```

- `plugin.json` — fields: `name` (`<name>`), `version` (`"0.1.0"`), `description`, `author` (`{ "name": "slickage" }`), `homepage` and `repository` (both `https://github.com/slickage/claude-plugins`), `license` (`MIT`), and a `keywords` array.
- `README.md` — a one-paragraph summary of the plugin.

Then add a section for the plugin to the **root** `README.md` so `bin/sync-versions.sh` can track its version (it warns otherwise). Under `## Available Plugins`, before that section's trailing `---`, insert:

```markdown
### <name> (v0.1.0)

<one-line description>. Install: `/plugin install <name>@slickage`.

---
```

`plugin.json` is the version source of truth — set it to `0.1.0`. NEVER hand-edit versions in `marketplace.json`; let `bin/sync-versions.sh` derive them.

On top of the common files, create the type-specific files:

#### Type `skill`

```
plugins/<name>/skills/<name>/SKILL.md
```

- `skills/<name>/SKILL.md` — the content from Step 3 (describe mode builds it; import mode copies it).

#### Type `hook`

```
plugins/<name>/hooks/hooks.json
plugins/<name>/scripts/<name>.sh
```

- `scripts/<name>.sh` — the script the hook runs. In describe mode, write the shell command / one-line script body from Step 3 into it (with a `#!/usr/bin/env bash` shebang). Mark it executable: `chmod +x plugins/<name>/scripts/<name>.sh`.
- `hooks/hooks.json` — registers the script as a `command`-type hook under the chosen event (and matcher, if any) from Step 3. Reference the script via `"${CLAUDE_PLUGIN_ROOT}"/scripts/<name>.sh`. Shape:

  ```json
  {
    "hooks": {
      "<EVENT>": [
        { "matcher": "<matcher or omit>", "hooks": [ { "type": "command", "command": "\"${CLAUDE_PLUGIN_ROOT}\"/scripts/<name>.sh" } ] }
      ]
    }
  }
  ```

  Omit the `"matcher"` key entirely when the chosen event takes no matcher (or the author left it blank). In import mode, copy the existing `hooks.json` as-is.

#### Type `mcp`

```
plugins/<name>/.mcp.json
```

- `.mcp.json` — declares the MCP server. In describe mode, use the shape that matches the author's Step 3 answer:

  Remote (HTTPS URL):

  ```json
  { "mcpServers": { "<name>": { "url": "<https url>", "headers": { "Authorization": "Bearer ${user_config.TOKEN}" } } } }
  ```

  Local (launch command):

  ```json
  { "mcpServers": { "<name>": { "command": "<cmd>", "args": [ ] } } }
  ```

  For every secret env var the author named in Step 3, add a corresponding `userConfig` entry to `plugin.json` marked `sensitive` so the secret is supplied by the user and NEVER shipped in the repo. Reference each secret only as `${user_config.KEY}` (e.g. a `TOKEN` secret is referenced as `${user_config.TOKEN}`). NEVER write secret VALUES into the repo — only the variable names and `${user_config.KEY}` references. In import mode, copy the existing `.mcp.json` as-is.

### Step 5: Sync, branch, commit, push, PR

Run:

```bash
bin/sync-versions.sh
git checkout -b feat/plugin-<name>
git add plugins/<name> .claude-plugin/marketplace.json README.md
git commit -m "feat(<name>): add plugin via /slickage-skill:new"
git push -u origin feat/plugin-<name>
gh pr create --fill --title "feat(<name>): add plugin" --body "Scaffolded via /slickage-skill:new. Review = quality gate."
```

If `bin/sync-versions.sh` fails, abort and report; leave the tree clean.

### Step 6: Present results

```
--- Plugin scaffolded ---
  Name:    <name>
  Type:    skill | hook | mcp
  Mode:    describe | import
  Branch:  feat/plugin-<name>
  PR:      <url>
-------------------------
```
