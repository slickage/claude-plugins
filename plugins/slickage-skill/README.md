# slickage-skill

The two halves of the Slickage catalog workflow — **authoring** new entries and
**consuming** the team's endorsed set.

- **`/slickage-skill:new <name> [--type skill|hook|mcp] [--from <path>]`** — scaffold a
  conformant plugin (a skill, a hook, or an MCP server) into this repo, either from a
  short description or by importing a local folder, then sync `marketplace.json` and
  open a PR.
  - `skill` (default) → `skills/<name>/SKILL.md`
  - `hook` → `hooks/hooks.json` + `scripts/<name>.sh`
  - `mcp` → `.mcp.json` (secrets declared as `userConfig`, never shipped)
- **`/slickage-skill:sync [--scope user|project|local] [--dry-run]`** — read the team's
  Notion catalog (Skills, MCP, Hooks), diff it against what you already have installed
  (`claude plugin list`), present the missing entries as a checklist, and
  `claude plugin install` the ones you pick. True manual entries (e.g. `brew`-installed
  CLI tools, hand-wired `settings.json` hooks) are surfaced as instructions, not
  auto-installed.

**Prerequisites:** `new` needs `gh` authenticated and a clone of
`slickage/claude-plugins`. `sync` needs the **Notion MCP** connected and the `claude`
CLI on PATH. PR review is the quality gate for authored entries.
