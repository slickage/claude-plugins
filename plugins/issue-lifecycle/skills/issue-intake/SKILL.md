---
name: Issue Intake
description: This skill should be used when the user asks to "turn these requirements into an issue", "create an issue and sub-issues", "scaffold an issue from this PRD/spec", "break these requirements into tickets", or hands over requirements to be filed in Linear or Jira. Creates a main tracker issue plus 1:1 linked sub-issues and BEADS tasks (the scaffold), then offers to hand off to the Issue Lifecycle skill for implementation.
version: 3.0.0
---

# Issue Intake

Turn requirements into a fully-scaffolded tracker issue: a main issue in Linear or Jira, one sub-issue per actionable unit, a parent BEADS task for the overarching issue, and one child BEADS task per sub-issue — all linked 1:1. Stop at the scaffold and offer to hand off to the **Issue Lifecycle** skill for implementation.

This skill **only does intake**. It does not implement, branch, or commit. Implementation is the Lifecycle skill's job.

## Operating principles

- **Create immediately; clarify only when blocked.** Read the requirements, research the codebase, and create the scaffold without a Q&A round — unless the requirements are too thin to break down sensibly, in which case ask targeted questions first.
- **Stop, then offer handoff.** After scaffolding, show the result and ask whether to run the Issue Lifecycle skill now. This single prompt is the deliberate gate between "issue created" and "code starts".
- **Shared core.** Use the same breakdown-and-link logic as the Lifecycle skill — the only difference is the input (requirements vs an existing issue) and that intake also creates the main issue.

## Shared references — read before acting

- **`../shared/resolution.md`** — infer provider + destination at runtime (no config file).
- **`../shared/providers.md`** — the provider contract; `createIssue` / `createSubIssue` / `listDestinations`.
- **`../shared/breakdown-and-link.md`** — the breakdown + sub-issue + parent-task core (use the **invent** path).

## Procedure

### 1. Resolve provider

Infer the active tracker per `resolution.md` (explicit mention → existing repo artifacts → connected MCP → ask). If the resolved provider's MCP is not connected, stop with a clear message.

### 2. Gather requirements

Collect requirements from, in priority order: command arguments, a referenced file/PRD path, or the current conversation context. Summarize what was understood in one or two sentences.

### 3. Research the codebase

Use Glob/Grep/Read to ground the breakdown in the real codebase (relevant files, patterns, architecture). Reference project skills in `.claude/skills/` if present.

### 4. Clarify only if blocked

If the requirements are too vague to produce a sensible main issue + breakdown (unclear scope, multiple incompatible interpretations, missing core behavior), use `AskUserQuestion` with targeted multiple-choice questions. Otherwise proceed without asking.

### 5. Resolve the destination

Decide *where* the issue is created per `resolution.md`: a team/project named in the invocation → else infer from a recent issue already created in this repo (via a BEADS `external-ref` → `getIssue` → its team/project) → else `listDestinations()` and have the user pick with `AskUserQuestion`. Resolve the choice to the native id via `resolveDestination`. Do not write the choice to a file — the created issue is the signal next time.

### 6. Create the main issue

`createIssue(title, description, type, destination)` via the provider. Derive a clear title and a description capturing the requirements, scope, and acceptance criteria. Set the type/label so the Lifecycle skill picks the right branch prefix later (Feature/Story, Bug, Chore, Documentation). Save the new issue id/key + URL.

### 7. Scaffold sub-issues + BEADS tasks

Run `../shared/breakdown-and-link.md` against the new issue using the **invent** path: init BEADS if needed, create the parent BEADS task (paired to the main issue), create child tasks, create + link one sub-issue per child (1:1), set parent-blocked-by-children dependencies. Write `docs/plans/<ISSUE-ID>.md` as the human-readable plan.

### 8. Show the scaffold

```
--- Issue Scaffolded ---
  Provider: <linear|jira>
  Issue:    <ISSUE-ID> — <Title>   <url>
  Sub-issues / tasks:
    <sub-id>  <task-id>  <title>
    <sub-id>  <task-id>  <title>
    ...
  Parent task: <parent-task-id>
  Plan: docs/plans/<ISSUE-ID>.md
------------------------
```

### 9. Offer handoff

Use `AskUserQuestion`: "Scaffold ready for `<ISSUE-ID>`. Run the Issue Lifecycle skill now to start implementation?"

- **Yes** → invoke the **Issue Lifecycle** skill on `<ISSUE-ID>`. Its guards will see the scaffold already exists and go straight to implementation.
- **No** → stop. The issue is review-ready; the user can run the Lifecycle skill later.
