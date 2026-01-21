---
description: Quick tech stack overview without generating files
---

# Quick Tech Stack Overview

Get an instant overview of the project's technology stack without generating skill files.

## Process

**Spawn the tech-stack-detector agent** for a rapid analysis.

Use the Task tool:
```
subagent_type: stackgen:tech-stack-detector
prompt: Perform a quick analysis of this codebase. Return a concise tech stack summary formatted for display. Do NOT generate any files - just return the analysis.
```

## Expected Output Format

The agent should return a summary like:

```
## Tech Stack

### Framework
- **Runtime:** Node.js 20.x
- **Framework:** Next.js 15.x (App Router)
- **Language:** TypeScript 5.x (strict mode)

### Frontend
- **UI Library:** React 19
- **Styling:** Tailwind CSS
- **Components:** shadcn/ui

### Backend
- **API Style:** Server Actions
- **Database:** PostgreSQL via Drizzle
- **Auth:** Clerk

### Infrastructure
- **Package Manager:** pnpm
- **Deployment:** Vercel
- **CI/CD:** GitHub Actions

### Testing
- **Unit:** Vitest
- **E2E:** Playwright

### Key Commands
- `dev` - Start development server
- `build` - Build for production
- `test` - Run tests
- `lint` - Run linter
```

## After Output

Check if `.claude/skills/` exists:
- If skills exist: List them and note they're available
- If no skills: Suggest running `/stackgen:analyze` for full skill generation
