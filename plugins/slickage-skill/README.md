# slickage-skill

The ingest + catalog-sync on-ramp for the Slickage skill catalog. Two commands:

- **`/slickage-skill:new <name> [--type skill|hook|mcp] [--from <path>]`** — scaffold a
  conformant plugin (a skill, a hook, or an MCP server), either from a short
  description or by importing an existing local folder. Registers it in
  `marketplace.json` via `bin/sync-versions.sh`, branches, and opens a PR with `gh`.
  - `skill` (default) → `skills/<name>/SKILL.md`
  - `hook` → `hooks/hooks.json` + `scripts/<name>.sh`
  - `mcp` → `.mcp.json` (secrets declared as `userConfig`, never shipped)
- **`/slickage-skill:sync-catalog [--dry-run]`** — regenerate the README `## Catalog`
  tables (Skills, MCP, Hooks) from the Notion catalog and open a PR. Refresh after a
  Notion edit. Requires the Notion MCP connected in the session.

**Prerequisites:** `gh` installed and authenticated; run from a clone of
`slickage/claude-plugins`. `sync-catalog` additionally needs the Notion MCP. PR
review is the quality gate.
