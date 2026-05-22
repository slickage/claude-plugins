# CONTEXT — Issue Lifecycle

Glossary for the issue-tracking standardization work. Terms only — no implementation detail.

## Tracker / Provider
The external issue-tracking system: **Linear** or **Jira**. The skills talk to it through a single **provider** contract (`getIssue`, `updateState`, `createIssue`, `createSubIssue`, `comment`, `listDestinations`, `resolveDestination`) so the same workflow runs against either. There is **no config file** — the provider and destination are inferred at runtime (explicit mention → existing repo issues → connected MCP → ask). Linear is backed by the existing Linear MCP; Jira by the Atlassian Remote MCP. If the resolved provider isn't wired, the skills refuse rather than half-work.

## Adopt
When the **Lifecycle skill** meets a human-authored main issue that already has sub-issues but no BEADS tasks, it **adopts** them — creating one BEADS task per existing sub-issue (1:1) rather than inventing a competing breakdown. It only invents a breakdown when the issue has no children.

## Issue
A unit of work as it exists in the tracker (Linear issue / Jira issue). The "main issue" is the parent that work is organized under.

## Sub-issue
A child issue in the tracker, nested under the main issue. One sub-issue corresponds to one **BEADS task**. (Linear: sub-issue. Jira: sub-task / child issue.)

## BEADS task
A locally-tracked, actionable unit of work in the BEADS database (`bd`). Each maps 1:1 to a **sub-issue**.

## Parent BEADS task (overarching task)
A single BEADS task representing the **main issue** as a whole. The child BEADS tasks roll up under it.

## Scaffold
The state produced by the **Intake skill**: main issue + sub-issues + BEADS tasks all created and linked, but no implementation done yet. Review-ready.

## Lifecycle skill (Skill A)
Given an existing issue, ensures a breakdown exists (creates it if missing), then implements every task and opens the PR. **Resumable single pass** — runs autonomously with zero mid-run confirmation; every step is guarded so re-invoking resumes where it left off. On an unrecoverable test failure it **stops and holds**: changes kept, task left in-progress, reported. Per-task commit + task-close is shared logic folded in from the old `/commit`.

## Intake skill (Skill B)
Given requirements (from args, a referenced file, or conversation context), researches the codebase, then creates the main issue + sub-issues + BEADS tasks (the **scaffold**), stops, and offers to hand off to the **Lifecycle skill**. Creates immediately; only pauses to clarify when requirements are too thin to break down.

## Breakdown-and-link (shared core)
The logic both skills share: split work into BEADS tasks, create matching sub-issues, link them, and create the parent BEADS task.

The mapping is strictly **1:1** — one child BEADS task per sub-issue. The **parent BEADS task** pairs with the existing main issue (no extra tracker entity is created for it) and is blocked by every child task, so it closes last. Closing all children unblocks the parent; closing the parent means the issue is done.
