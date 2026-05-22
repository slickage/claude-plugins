# BEADS is the source of truth; tracker sync is one-way push

During execution, the Lifecycle skill treats the local BEADS database as authoritative and pushes state changes outward to the tracker (Linear/Jira) only — claiming a child task sets its sub-issue *In Progress*, closing it sets *Done*, and closing the parent task sets the main issue *In Review*. The tracker is a mirror for humans to watch, never read back mid-run except the initial issue fetch.

## Considered Options

- **One-way push (chosen).** Simple, deterministic, no reconciliation. Mid-run edits a human makes in the tracker are not honored until the next fresh run (where existing sub-issues are *adopted*).
- **Bidirectional reconcile (rejected).** On each re-invocation, read tracker state and reconcile against BEADS (e.g. a human closing a sub-issue marks its task done). More accurate for human-in-the-loop teams but materially more complex, and nothing in this environment can subscribe to Linear/Jira webhooks — it would require polling and conflict resolution.

## Consequences

A reader will wonder why human edits to sub-issues are ignored during a run; the answer is the rejected bidirectional option above. Moving to bidirectional later is real rework, not a flag flip — the provider contract, guards, and loop would all need a read-reconcile phase.
