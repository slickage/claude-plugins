# Provider abstraction

Both skills talk to the tracker through one contract so the same workflow runs against Linear or Jira. Resolve the active provider from `config.md` first, then call the contract operations — never call a tracker's MCP tools directly from the skill body.

## The contract

| Operation | Purpose |
|---|---|
| `getIssue(id)` | Fetch title, description, labels/type, URL, state, native UUID/key, and existing children. |
| `listSubIssues(id)` | Return the issue's existing child issues (id, title, state). Used by **adopt**. |
| `createIssue(title, description, type)` | Create a top-level issue. Returns its id/key + URL. |
| `createSubIssue(parentId, title, description)` | Create a child issue nested under `parentId`. Returns its id/key + URL. |
| `updateState(id, state)` | Transition an issue to `inProgress` / `inReview` / `done`. |
| `comment(id, body)` | Post a comment on an issue. |

## Linear implementation

Backed by the Linear MCP already present in the environment (`mcp__plugin_linear_linear__*`).

| Operation | Linear MCP |
|---|---|
| `getIssue` | `mcp__plugin_linear_linear__get_issue` (save the `id` UUID, `url`, `labels`) |
| `listSubIssues` | `mcp__plugin_linear_linear__list_issues` filtered by `parentId` |
| `createIssue` | `mcp__plugin_linear_linear__create_issue` (set `teamId` from `linear.teamKey`) |
| `createSubIssue` | `mcp__plugin_linear_linear__create_issue` with `parentId` = main issue UUID |
| `updateState` | `mcp__plugin_linear_linear__update_issue` with `state` = `"In Progress"` / `"In Review"` |
| `comment` | `mcp__plugin_linear_linear__create_comment` |

Linear closes the main issue automatically when the linked PR (`Closes <ISSUE-ID>` in the body) merges — do not force a `done` transition.

## Jira implementation

Backed by the **Atlassian Remote MCP** (official), which the user installs and authenticates separately. The exact tool names depend on the installed Atlassian MCP build — **confirm the connected tool names at runtime** rather than assuming. Map the contract to the Atlassian MCP's create / get / transition / comment / search tools:

| Operation | Atlassian MCP (typical) |
|---|---|
| `getIssue` | get-issue / read by key; capture `key`, `fields.summary`, `fields.status`, `self` URL |
| `listSubIssues` | search JQL `parent = <KEY>` (or read `fields.subtasks`) |
| `createIssue` | create-issue with `projectKey`, `issuetype` derived from type |
| `createSubIssue` | create-issue with `issuetype` = `jira.subtaskIssueType` and `fields.parent.key` = main key |
| `updateState` | transition-issue to the configured `jira.states.*` name |
| `comment` | add-comment |

Jira workflow state names are per-project — always use the names from `jira.states` in config, never hardcode `"In Progress"`.

## Failure rule

If the configured provider's MCP is not connected, stop with a clear message. Never half-work or fall back to the other provider.
