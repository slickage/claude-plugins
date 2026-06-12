---
description: Regenerate the README Catalog tables from the Notion catalog (Skills, MCP, Hooks) and open a PR
argument-hint: "[--dry-run]"
allowed-tools: Bash(git:*), Bash(gh:*), Bash(python3:*), mcp__plugin_Notion_notion__notion-search, mcp__plugin_Notion_notion__notion-fetch
---

## Context

Dynamic values injected at runtime:

- Repo root check: !`git rev-parse --show-toplevel 2>/dev/null || echo "NOT_A_GIT_REPO"`
- Origin: !`git remote get-url origin 2>/dev/null || echo "NO_ORIGIN"`
- gh auth: !`gh auth status 2>&1 | head -1 || echo "GH_UNAUTHENTICATED"`
- Catalog markers present: !`grep -c "catalog:.*:start" README.md 2>/dev/null || echo 0`

## Instructions

You regenerate the three Catalog tables in `README.md` from the Notion catalog data sources, then open a PR with the changes. Only the text between the HTML comment markers is rewritten; everything else in `README.md` stays byte-for-byte. Use `$ARGUMENTS` as the raw user input.

### Step 1: Preflight guards

Refuse cleanly and stop if ANY condition below holds. Do NOT half-write files; leave the working tree clean. Output a single-line reason and stop.

- The origin (from Context) does not point at `slickage/claude-plugins`.
- `gh` is not authenticated (the Context gh-auth line shows `GH_UNAUTHENTICATED` or any failure message).
- `README.md` is missing the catalog markers — the Context "Catalog markers present" line shows fewer than 6 marker lines (there are 3 start + 3 end markers).
- The Notion tools (`mcp__plugin_Notion_notion__notion-search` / `mcp__plugin_Notion_notion__notion-fetch`) are not available in this session. If a Notion call errors as unknown or unavailable, stop and tell the user to connect the Notion MCP.

### Step 2: Fetch rows from the three Notion data sources

For EACH data source below: call `mcp__plugin_Notion_notion__notion-search` with `data_source_url` set to the collection URL, a generic query, `page_size` 25, and `max_highlight_length` 0 to list the row page IDs; then call `mcp__plugin_Notion_notion__notion-fetch` on each row id to read its `<properties>` JSON.

- **Skills**: `collection://4c032e9f-e7b1-4181-87a7-0ade9d821351` — columns: `Skill`, `Category`, `Install`, `Source`, `What it does`, `When to use`.
- **MCP Integrations**: `collection://6102c793-cb36-4344-b911-d075e419b5c7` — columns: `Integration`, `Install`, `Source`, `What it does`, `When to use`.
- **Hooks & Tools**: `collection://c7feed0e-d455-45c0-ac5b-6e9e1d4e6c0f` — columns: `Name`, `Type`, `Install / Config`, `Source`, `Trigger`, `What it does`.

### Step 3: Classify (Skills and MCP only)

A row is **Hosted here** if its `Install` value contains `@slickage` OR a directory `plugins/<kebab-name>/` exists in the repo (derive `<kebab-name>` from the title). Otherwise it is **Endorsed elsewhere**.

### Step 4: Build the markdown for each section

- **Skills** (between `<!-- catalog:skills:start -->` and `<!-- catalog:skills:end -->`): a `**Hosted here** — after `/plugin marketplace add slickage/claude-plugins`:` line followed by a table (`Skill | Install | What it does`), then a `**Endorsed elsewhere** — install from their own source:` line followed by a table. Use the row's `Install` value verbatim.
- **MCP** (between `<!-- catalog:mcp:start -->` and `<!-- catalog:mcp:end -->`): one table (`Integration | Install | What it does`). If any MCP row is hosted here, split it into Hosted/Endorsed like Skills; today none are.
- **Hooks** (between `<!-- catalog:hooks:start -->` and `<!-- catalog:hooks:end -->`): one table (`Name | Type | Install / Config | What it does`).

Escape pipe characters (`|` → `\|`) inside cell text. Keep each description to one line.

### Step 5: Rewrite README.md between the markers ONLY

Replace only the text strictly between each start/end marker pair, leaving the marker lines themselves in place and every other part of the file untouched. Do this with a `python3` heredoc that, for each `(start_marker, end_marker, new_block)` triple, splices `new_block` between the two marker lines. For example:

```bash
python3 <<'PY'
import re

path = "README.md"
src = open(path, encoding="utf-8").read()

replacements = [
    ("<!-- catalog:skills:start -->", "<!-- catalog:skills:end -->", SKILLS_BLOCK),
    ("<!-- catalog:mcp:start -->",    "<!-- catalog:mcp:end -->",    MCP_BLOCK),
    ("<!-- catalog:hooks:start -->",  "<!-- catalog:hooks:end -->",  HOOKS_BLOCK),
]

for start, end, block in replacements:
    pattern = re.compile(
        re.escape(start) + r".*?" + re.escape(end),
        re.DOTALL,
    )
    src = pattern.sub(start + "\n" + block.strip() + "\n" + end, src, count=1)

open(path, "w", encoding="utf-8").write(src)
PY
```

Build `SKILLS_BLOCK`, `MCP_BLOCK`, and `HOOKS_BLOCK` from the markdown you assembled in Step 4. Do not touch any other part of the file.

### Step 6: Dry-run or commit

If `$ARGUMENTS` contains `--dry-run`: show `git diff README.md` and stop — no branch, no commit, no PR.

Otherwise, if `git diff --quiet README.md` reports no change, print `Catalog already in sync.` and stop. Else run:

```bash
git checkout -b chore/sync-catalog
git add README.md
git commit -m "docs(catalog): sync README catalog from Notion"
git push -u origin chore/sync-catalog
gh pr create --fill --title "docs(catalog): sync from Notion" --body "Regenerated by /slickage-skill:sync-catalog."
```

### Step 7: Present results

```
--- Catalog synced ---
  Skills:  <n> (hosted <h> / external <e>)
  MCP:     <n>
  Hooks:   <n>
  PR:      <url or "dry-run, no PR">
----------------------
```
