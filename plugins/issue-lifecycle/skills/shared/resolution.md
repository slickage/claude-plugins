# Provider & destination resolution

No config file. Both skills infer **which tracker** (provider) and **where in it** (destination) at runtime from observable signals, and ask the user only when nothing can be inferred. "Previous calls" means the issues already created in this repo — re-inferred each run, never stored separately.

## Provider resolution (both skills)

Decide Linear vs Jira, first hit wins:

1. **Explicit in the invocation** — the user named a tracker ("file this in Jira") or passed an issue key whose tracker is unambiguous.
2. **Existing repo artifacts** — infer from prior work in this repo:
   - BEADS task `external-ref` values (`bd list --all`): Linear refs are UUIDs / `TEAM-123` issue ids; Jira refs are `PROJ-123` keys tied to the Jira MCP.
   - The issue ID the Lifecycle skill was given, the current branch's `<prefix>/<issue-id>-<slug>`, or `docs/plans/*.md`.
3. **Connected MCP** — if exactly one tracker MCP is connected this session (Linear MCP `mcp__plugin_linear_linear__*` vs the Atlassian/Jira MCP), use it.
4. **Ask** — if still ambiguous (e.g. both MCPs connected, no prior artifacts), `AskUserQuestion` to pick the provider.

If the resolved provider's MCP is **not connected**, stop with a clear message (e.g. "Jira inferred but no Atlassian MCP is connected — install/auth it"). Never silently fall back to the other tracker.

## Destination resolution (Intake skill only)

The Lifecycle skill works on an existing issue and **inherits** its team/project — it never resolves a destination. Intake creates top-level issues, so it resolves *where*, first hit wins:

1. **Per-call hint** — a team/project named in the invocation ("create in the Platform team", "under PROJ"). Resolve it to the native id via `resolveDestination(hint)`.
2. **Infer from prior issues in this repo** — take a recent issue created here (from a BEADS `external-ref` → `getIssue`), read its team/project, and reuse that destination. This is the "previous calls" path: the repo's own history is the memory.
3. **Ask** — if there's no hint and nothing to infer from (first issue in a fresh repo), call `listDestinations()`, present the options with `AskUserQuestion`, and let the user pick.

Do not persist the choice to a file. The created issue itself becomes the signal step 2 reads next time.

See `providers.md` for the `listDestinations` / `resolveDestination` contract and the per-tracker MCP mappings (including Linear's team-key → team-UUID requirement).
