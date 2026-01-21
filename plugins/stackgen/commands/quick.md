---
description: Quick tech stack overview without generating files
---

# Quick Tech Stack Overview

Get an instant overview without generating skill files.

## Process

**Spawn all 4 detectors in parallel using `model: haiku`:**

```
Task calls (all in ONE message):
- subagent_type: stackgen:dependency-detector, model: haiku
- subagent_type: stackgen:config-detector, model: haiku
- subagent_type: stackgen:structure-detector, model: haiku
- subagent_type: stackgen:pattern-detector, model: haiku
```

## Output

Merge results and display concise summary:

```
## Tech Stack

**Framework:** Next.js 15 (App Router) + React 19
**Language:** TypeScript (strict)
**Styling:** Tailwind + shadcn/ui
**Database:** PostgreSQL + Drizzle
**Auth:** Clerk
**State:** TanStack Query
**Testing:** Vitest + Playwright
**Deploy:** Vercel + GitHub Actions

**Commands:**
- dev: `pnpm dev`
- build: `pnpm build`
- test: `pnpm test`
```

## After Output

Check `.claude/skills/`:
- If exists: List available skills
- If not: Suggest `/stackgen:analyze`
