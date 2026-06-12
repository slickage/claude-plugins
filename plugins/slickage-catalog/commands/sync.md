---
description: Sync your machine to the team catalog — show endorsed entries you don't have yet and install the ones you pick
argument-hint: "[--scope user|project|local] [--dry-run]"
allowed-tools: Bash(claude:*), Bash(grep:*), Bash(cat:*), Bash(test:*), mcp__plugin_Notion_notion__notion-search, mcp__plugin_Notion_notion__notion-fetch
---

## Context

Dynamic values injected at runtime:

- claude CLI: !`claude --version 2>/dev/null || echo "NO_CLAUDE_CLI"`
- Installed plugins (JSON): !`claude plugin list --json 2>/dev/null || echo "[]"`
- Configured MCP servers: !`claude mcp list 2>/dev/null | sed 's/ - .*//' || echo ""`
- User hooks (raw): !`cat ~/.claude/settings.json 2>/dev/null | grep -i "hooks" -A0 || echo "none"`

## Instructions

You help a coworker bring their machine in line with the team catalog. The **Notion catalog** is the source of truth for what the team endorses; the coworker's machine already has some of it. You read the catalog, diff it against what they have installed, show them only the **missing** entries as a checklist, and install the ones they pick by running the `claude` CLI. Use `$ARGUMENTS` as the raw user input.

This command does NOT touch this repo or open a PR — it installs onto the coworker's own machine.

### Step 1: Parse arguments

- `--scope <user|project|local>` — install scope for `claude plugin install`. Default `user` (available across all their projects). `project` writes to the current repo's `.claude/settings.json` (shared); `local` is this-repo-only.
- `--dry-run` — show the exact `claude` commands that WOULD run, install nothing.

### Step 2: Preflight guards

Stop with a one-line reason if:

- The `claude` CLI is unavailable (Context shows `NO_CLAUDE_CLI`).
- The Notion tools (`mcp__plugin_Notion_notion__notion-search` / `mcp__plugin_Notion_notion__notion-fetch`) are not available in this session. If a Notion call errors as unknown/unavailable, stop and tell the coworker to connect the Notion MCP (`/plugin install notion@claude-plugins-official`, then authenticate), since the catalog lives in Notion.

### Step 3: Read the team catalog from Notion

For EACH data source below, call `mcp__plugin_Notion_notion__notion-search` with `data_source_url` set to the collection URL, a generic query, `page_size` 25, `max_highlight_length` 0 to list row page IDs; then `mcp__plugin_Notion_notion__notion-fetch` each row for its `<properties>`.

- **Skills**: `collection://4c032e9f-e7b1-4181-87a7-0ade9d821351` — `Skill`, `Install`, `Source`, `What it does`
- **MCP Integrations**: `collection://6102c793-cb36-4344-b911-d075e419b5c7` — `Integration`, `Install`, `Source`, `What it does`
- **Hooks & Tools**: `collection://c7feed0e-d455-45c0-ac5b-6e9e1d4e6c0f` — `Name`, `Type`, `Install / Config`, `Source`, `What it does`

Build a list of catalog entries, each: `{ name, type (skill|mcp|hook), install_string, what }`.

### Step 4: Classify each entry as plugin vs manual

Look at the entry's install string:

- **Plugin entry** — the install string contains a `/plugin install <plugin>@<marketplace>` (optionally preceded by `/plugin marketplace add <source>`). This covers nearly everything, including the MCP rows (they install as plugins) and plugin-packaged hooks (e.g. `caveman`, `greptile`). Extract:
  - `plugin_id` = `<plugin>@<marketplace>`
  - `marketplace_source` = the `<source>` from any `marketplace add` step (may be absent for the default `claude-plugins-official`).
- **Manual entry** — the install string is NOT a `/plugin` command (e.g. `Finish sound` → a `Stop` hook + `afplay`, `rtk` → `brew install` + a settings.json hook). These can't be cleanly auto-installed.

### Step 5: Detect what's already installed

Parse the Context "Installed plugins (JSON)" — an array of `{ "id": "<plugin>@<marketplace>", "enabled": ... }`. Build a set of installed plugin ids. Match **case-insensitively** on the plugin part (the catalog may say `notion`, the install id may be `Notion@...`).

A plugin entry is **installed** if its `plugin_id`'s plugin part matches an installed id. A manual entry is **installed** if a recognizable signature appears in the Context "Configured MCP servers" / "User hooks (raw)" lines (best-effort; if unsure, treat as not-installed and let the coworker judge).

### Step 6: Compute the gap

`missing = catalog entries not detected as installed`, split into:
- **missing_plugins** (auto-installable)
- **missing_manual** (need manual steps)

If both are empty: print `✓ You're in sync with the team catalog — nothing new.` and stop.

### Step 7: Present the checklist

Use `AskUserQuestion` with `multiSelect: true` to let the coworker pick which **missing_plugins** to install. Group by type so it reads cleanly — one question per type that has missing entries (Skills / MCP / Hooks). Each option label is the entry `name`; put its one-line `what` in the option description.

`AskUserQuestion` allows at most 4 options per question. If a type has more than 4 missing entries, ask additional questions for the overflow (e.g. "More skills (2 of 2)"). Offer every missing plugin across however many questions it takes — never silently drop entries.

Do NOT put manual entries in the checklist; handle them in Step 9.

### Step 8: Install the picked plugins

Ask the coworker once for the scope if `--scope` wasn't given (`AskUserQuestion`, options user / project / local, default user). Then for each picked entry, in order:

```bash
# only if the entry had a marketplace-add step and that marketplace isn't already added:
claude plugin marketplace add <marketplace_source>
# then:
claude plugin install <plugin>@<marketplace> --scope <scope>
```

If `--dry-run`, print these commands instead of running them.

Run the `marketplace add` for an entry before its `install`. Skip a `marketplace add` that's already configured (it's idempotent, so re-running is harmless if unsure). Report any install that errors but keep going with the rest.

### Step 9: Surface manual entries

For each **missing_manual** entry the coworker is interested in, print its `Install / Config` text verbatim from the catalog — these involve `brew install` and/or hand-editing `~/.claude/settings.json`, which this command will NOT do automatically. Let the coworker apply them.

### Step 10: Report

```
--- Catalog sync ---
  Installed now:   <names, or none>
  Already had:     <count>
  Manual (do yourself): <names, or none>
  Scope:           <scope>
--------------------
Restart Claude Code to load newly installed plugins.
```

If `--dry-run`, say so and that nothing was changed.
