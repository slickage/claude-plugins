# Repo configuration

Both skills read a repo-level config file to decide which tracker (provider) to talk to. The provider is **never** guessed from issue-ID format — Linear `ONC-5` and Jira `PROJ-123` are indistinguishable.

## Location

`.issue-lifecycle.json` at the repository root.

## Schema

```json
{
  "provider": "linear",
  "linear": {
    "teamKey": "ONC"
  },
  "jira": {
    "cloudId": "your-atlassian-cloud-id",
    "projectKey": "PROJ",
    "subtaskIssueType": "Sub-task",
    "states": {
      "inProgress": "In Progress",
      "inReview": "In Review",
      "done": "Done"
    }
  }
}
```

- `provider` (required) — `"linear"` or `"jira"`. Selects the active provider.
- `linear.teamKey` — Linear team key, used to scope issue creation.
- `jira.cloudId` / `jira.projectKey` — required for the Atlassian MCP.
- `jira.subtaskIssueType` — issue type used for sub-issues (Jira calls them sub-tasks). Defaults to `"Sub-task"`.
- `jira.states` — names of the workflow states to transition to. Jira workflows are per-project and configurable, so these must be declared.

## Resolution rules

1. Read `.issue-lifecycle.json` at the repo root.
2. If the file is missing, default `provider` to `"linear"` (the historically-supported tracker) and proceed; warn that no config was found.
3. If `provider` is `"jira"` but no Jira MCP is connected (verify the Atlassian MCP tools are available in the session), **stop** and tell the user to install/auth the Atlassian Remote MCP. Do not silently fall back to Linear.
4. Cache the resolved provider for the whole run.

See `providers.md` for what each provider must implement.
