# Provider abstraction

Both skills talk to the tracker through one contract so the same workflow runs against Linear or Jira. Resolve the active provider and destination at runtime per `resolution.md` first, then call the contract operations — never call a tracker's MCP tools directly from the skill body.

## The contract

| Operation | Purpose |
|---|---|
| `getIssue(id)` | Fetch title, description, labels/type, URL, state, native UUID/key, and existing children. |
| `listSubIssues(id)` | Return the issue's existing child issues (id, title, state). Used by **adopt**. |
| `listDestinations()` | List the destinations where a top-level issue can be created (Linear teams/projects, Jira projects), each with a stable id + display name. Used when a destination must be resolved interactively. |
| `resolveDestination(hint?)` | Turn a per-call hint or an inferred team/project (key or name) into the native id `createIssue` needs. Returns null if it can't be resolved unambiguously. |
| `createIssue(title, description, type, destination)` | Create a top-level issue in the resolved destination. Returns its id/key + URL. |
| `createSubIssue(parentId, title, description)` | Create a child issue nested under `parentId`. Returns its id/key + URL. |
| `updateState(id, state)` | Transition an issue to `inProgress` / `inReview` / `done`. |
| `comment(id, body)` | Post a comment on an issue. |

## Linear implementation

Backed by the Linear MCP already present in the environment (`mcp__plugin_linear_linear__*`).

| Operation | Linear MCP |
|---|---|
| `getIssue` | `mcp__plugin_linear_linear__get_issue` (save the `id` UUID, `url`, `labels`) |
| `listSubIssues` | `mcp__plugin_linear_linear__list_issues` filtered by `parentId` |
| `listDestinations` | `mcp__plugin_linear_linear__list_teams` (and `list_projects` for the chosen team) |
| `resolveDestination` | Match the team key/name (from the invocation hint or inferred from a prior issue) against `list_teams` to get the team **UUID** (`teamId`) — the key alone is not accepted by `create_issue`. If a project is given/inferred, validate it via `list_projects`. |
| `createIssue` | `mcp__plugin_linear_linear__create_issue` with `teamId` = resolved team UUID (and `projectId` if resolved) |
| `createSubIssue` | `mcp__plugin_linear_linear__create_issue` with `parentId` = main issue UUID (inherits the parent's team) |
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
| `createSubIssue` | create-issue with the sub-task issue type (default `"Sub-task"`) and `fields.parent.key` = main key |
| `updateState` | transition-issue — discover the available transitions for the issue at runtime and match the intended phase (In Progress / In Review / Done) to the closest workflow status name |

Jira workflow state names are per-project and not knowable ahead of time — **read the issue's available transitions at runtime** and map the intended phase to the closest status. If no clear match exists, ask the user which status to use. Never hardcode `"In Progress"`.

## Failure rule

If the resolved provider's MCP is not connected, stop with a clear message. Never half-work or fall back to the other provider.
