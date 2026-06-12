# slickage-catalog

The two halves of the Slickage catalog workflow — **publish** entries to the team
catalog, and **sync** them onto your machine. The catalog's source of truth is the
Notion AI wiki ([AI] Claude Skills, MCP, Hooks); every entry there is a skill, an
MCP server, or a hook.

- **`/slickage-catalog:publish <name> [--type skill|hook|mcp] [--from <path>] [--source <upstream>]`**
  — add an entry to the catalog. It picks one of two paths automatically:
  - **HOST** (it's yours / local / unpublished — `--from <folder>` or a described new
    one): scaffold a conformant plugin into this repo, open a PR, and add a Notion row
    with `Install = …@slickage`.
    - `skill` (default) → `skills/<name>/SKILL.md`
    - `hook` → `hooks/hooks.json` + `scripts/<name>.sh`
    - `mcp` → `.mcp.json` (secrets declared as `userConfig`, never shipped)
  - **ENDORSE** (it's third-party / already published — `--source <upstream>`): no
    hosting, just add a Notion row pointing at the upstream install string. A
    third-party entry must have an install string; a local-only one routes to HOST.
- **`/slickage-catalog:sync [--scope user|project|local] [--dry-run]`** — read the team's
  Notion catalog (Skills, MCP, Hooks), diff it against what you already have installed
  (`claude plugin list`), present the missing entries as a checklist, and
  `claude plugin install` the ones you pick. True manual entries (e.g. `brew`-installed
  CLI tools, hand-wired `settings.json` hooks) are surfaced as instructions, not
  auto-installed.

**Prerequisites:** both commands need the **Notion MCP** connected. `publish`'s HOST
path also needs `gh` authenticated and a clone of `slickage/claude-plugins`; `sync`
also needs the `claude` CLI on PATH. PR review is the quality gate for hosted entries.
