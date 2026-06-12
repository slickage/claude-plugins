# SlickSkills — MVP Design (Catalog + Ingest)

**Date:** 2026-06-11
**Status:** Approved (brainstorming) — pending spec review
**Repo:** `slickage/claude-plugins`

## Problem

Slickage has no shared, low-friction way to discover and adopt Claude Code skills.
Today knowledge spreads by word of mouth, ad-hoc links, and a manually maintained
Notion catalog. There is no single place a coworker can browse what the team uses,
and no easy on-ramp for someone to contribute a skill they built.

Skillburst (skillburst.ai) frames the full vision — a governed distribution layer
for an org's AI skills, delivered through one connection, with ingest, governance,
distribution, and usage measurement. This MVP takes the smallest slice that
delivers real value on Slickage's existing infrastructure, Claude Code only.

## Goal

An internal Claude Code skill **catalog** plus a low-friction **ingest** path, built
on the existing `slickage/claude-plugins` marketplace. No servers, no telemetry,
no new SaaS. Access is gated automatically by GitHub org membership (the repo is
private to the Slickage org).

## Scope

### In scope (MVP)

**Part A — Catalog (mostly exists; adopt + curate + document)**
- `slickage/claude-plugins` is already a working plugin marketplace with
  `stackgen` and `issue-lifecycle`, valid `marketplace.json`, and a README with
  install instructions (`/plugin marketplace add slickage/claude-plugins`).
- Work: establish it as *the* team skill catalog — seed it with a curated set of
  skills worth sharing internally (drawn from the existing Notion catalog), and
  make the catalog discoverable (README "catalog" section + a pointer from the
  Notion AI wiki page).
- Distribution is native Claude Code: `/plugin install <skill>@slickage`.
- Access gate is automatic: private repo → only Slickage GitHub org members can add
  the marketplace. Nothing to build.

**Part B — Ingest command (the real net-new build)**
- A new plugin providing a `/slickage-skill new` slash command.
- Interactive flow: the author describes the skill (or points at an existing local
  skill folder); Claude scaffolds a conformant `SKILL.md` (+ `plugin.json` and the
  `plugins/<name>/` layout), adds the entry to `.claude-plugin/marketplace.json`,
  runs `bin/sync-versions.sh`, creates a branch, and opens a PR via `gh`.
- PR review is the lightweight quality gate (and the seed of v2 governance).
- Mirrors Skillburst's "write it in plain English, the assistant builds it" — with
  none of the SaaS.

### Out of scope (deferred)

- **Measure / usage telemetry** — deferred to v2. Design already sketched: a
  collector hook reads the GitHub username via `gh api user`, POSTs
  `{github_user, skill, ts}` to a small endpoint that verifies Slickage org
  membership server-side and writes to storage. Documented here so it is ready
  when wanted; not built in MVP.
- MCP server, OAuth/SSO (org membership is the gate).
- Cursor / other tools (Claude Code only).
- Approval automation, semantic-versioning UI, rollback UI (git already versions;
  rollback = `git revert`; review = PR review).
- Analytics dashboard.

## Architecture

```
Author (Claude Code)
  │  /slickage-skill new
  ▼
scaffold SKILL.md + plugin.json + plugins/<name>/        ── Ingest (Part B)
  │  update marketplace.json  +  bin/sync-versions.sh
  │  branch + gh pr create
  ▼
slickage/claude-plugins (PR review → merge)              ── Catalog (Part A)
  ▲
  │  /plugin marketplace add slickage/claude-plugins
  │  /plugin install <skill>@slickage
Consumer (Claude Code)   ── access gated by GitHub org membership (private repo)
```

### Components

- **Catalog repo** (`slickage/claude-plugins`, exists): source of truth. Each skill
  is a plugin folder under `plugins/<name>/` with `plugin.json` (version source of
  truth) and a `SKILL.md`. `marketplace.json` and `README.md` are derived via
  `bin/sync-versions.sh`.
- **Ingest plugin** (new, e.g. `plugins/skill-forge/`): one command,
  `commands/new.md`, following the existing command-authoring conventions
  (precise, literal instructions; see README "Writing Good Commands"). Depends on
  `gh` (already installed/authed in the team's environment) and
  `bin/sync-versions.sh`.

### Conventions to follow (from the existing repo)

- `plugin.json` is the version source of truth; never hand-edit versions in
  `marketplace.json` or `README.md` — run `bin/sync-versions.sh`.
- Plugin layout: `plugins/<name>/{skills,commands,agents}/` as needed.
- Design/plan docs live in `docs/plans/` as
  `YYYY-MM-DD-<topic>-{design,plan}.md` pairs.
- Bash 3.2 / BSD-sed compatible scripts; `python3` for JSON.

## Data flow / state

No runtime state, no database. The catalog repo's git history is the only store.
Versioning is git + `plugin.json` semver. The ingest command produces a PR; merge
makes a skill live; consumers pull via `/plugin`.

## Error handling

- Ingest command refuses (rather than half-writes) if: not inside a clone of
  `slickage/claude-plugins`, `gh` is not authenticated, the target skill name
  already exists, or `bin/sync-versions.sh` fails. Each failure reports clearly and
  leaves the working tree clean.
- A malformed scaffold is caught by PR review before it can reach `main`.

## Testing

- Manual acceptance: run `/slickage-skill new` end to end, confirm it produces a
  valid plugin folder, a synced `marketplace.json`, and an open PR; after merge,
  confirm `/plugin install <skill>@slickage` works in a fresh session.
- Lint: `bin/sync-versions.sh` runs clean and produces no diff on a no-op.

## Open questions / risks — RESOLVED

Both resolved in the implementation plan
(`2026-06-11-slickskills-catalog-ingest-plan.md`):

- **Ingest command depth → thin.** v1 scaffolds from a short description OR
  imports an existing local skill folder, then opens a PR. No conversational
  interview / chat-to-SKILL.md generation; enrich in v2.
- **Curation source → Notion "Skills" endorsed set** (data source
  `collection://4c032e9f-e7b1-4181-87a7-0ade9d821351`). Endorsed rows: `caveman`,
  `frontend-design`, `commit-commands`, `impeccable`, `issue-lifecycle`,
  `code-review`, `beads-tasks (bd)`, `Claude in Chrome`, `superpowers`. **Link,
  don't vendor:** 8 of 9 point to external sources and are referenced by their
  native install string; only `issue-lifecycle` is hosted here. Locally hosted
  set stays `stackgen` + `issue-lifecycle` + the new `slickage-skill` plugin.

## Addendum (2026-06-11) — multi-type ingest + catalog sync

Two scope expansions landed in the MVP PR, after confirming via the Claude Code
plugin docs that a single plugin can bundle skills, commands, agents, **hooks**
(`hooks/hooks.json` + scripts via `${CLAUDE_PLUGIN_ROOT}`), and **MCP servers**
(`.mcp.json`, local or remote; secrets via `userConfig` `sensitive: true`, never
shipped).

- **Multi-type ingest.** `/slickage-skill:new` gains `--type skill|hook|mcp`
  (default `skill`). Per-type scaffold: `skill` → `skills/<name>/SKILL.md`;
  `hook` → `hooks/hooks.json` + `scripts/<name>.sh`; `mcp` → `.mcp.json` with
  `userConfig` secret declarations. Import mode's required key file is per-type
  (`SKILL.md` / `hooks.json` / `.mcp.json`).
- **Catalog sync = install assistant.** New `/slickage-skill:sync [--scope] [--dry-run]`
  reads the team's Notion catalog (Skills, MCP, Hooks data sources), diffs it
  against what the coworker already has installed (`claude plugin list --json`),
  presents the missing entries as an `AskUserQuestion` checklist, and installs the
  picks via the `claude` CLI (`claude plugin marketplace add` + `claude plugin
  install --scope`). True manual entries (brew CLIs, hand-wired `settings.json`
  hooks) are surfaced as instructions, not auto-run. Notion is the source of
  truth; this command brings a machine in line with it.
  - *Rejected approach:* an earlier cut regenerated the README catalog table from
    Notion and opened a PR. Dropped — keeping a README mirror of Notion has no
    value; the point is to **install**, not to duplicate the list into docs.
  - Detection insight: nearly every catalog entry installs as a *plugin* (even the
    MCP rows), so the diff is `catalog plugin_ids − claude plugin list ids`.
- **Catalog scope.** The README keeps only the stable **Hosted here** table (our
  own plugins); the third-party set is browsed in Notion or pulled via `sync`,
  not mirrored into the README.

## Future (v2+)

- Measure (telemetry shim, designed above).
- Governance automation (required reviewers, CODEOWNERS on `plugins/`).
- Cross-tool distribution if Cursor/others get adopted.
