---
description: Quick tech stack overview without generating files
---

# Quick Tech Stack Overview

Get an instant overview of the project's technology stack without generating skill files.

## Process

**Spawn a single stack-detector agent** using the Task tool with `model: haiku`:

```
subagent_type: stackgen:stack-detector
model: haiku
prompt: Quick analysis of the codebase. Return concise tech stack summary as JSON.
```

## Display Results

After the detector completes, format and display:

```
## Tech Stack

### Framework
- **Runtime:** [Node.js, Python, etc.]
- **Framework:** [Next.js, Express, etc.]
- **Language:** [TypeScript, JavaScript, etc.]

### Frontend
- **UI Library:** [React, Vue, etc.]
- **Styling:** [Tailwind, CSS Modules, etc.]
- **Components:** [shadcn/ui, MUI, etc.]

### Backend
- **API Style:** [Server Actions, REST, GraphQL, etc.]
- **Database:** [PostgreSQL, MongoDB, etc.]
- **ORM:** [Drizzle, Prisma, etc.]
- **Auth:** [Clerk, NextAuth, etc.]

### Architecture
- **Style:** [Feature-based, layer-based, etc.]
- **Organization:** [App Router, Pages Router, etc.]

### Infrastructure
- **Package Manager:** [pnpm, npm, yarn]
- **Deployment:** [Vercel, AWS, etc.]
- **CI/CD:** [GitHub Actions, etc.]

### Testing
- **Unit:** [Vitest, Jest, etc.]
- **E2E:** [Playwright, Cypress, etc.]

### Key Commands
- `dev` - [Start development server]
- `build` - [Build for production]
- `test` - [Run tests]
```

## After Output

Check if `.claude/skills/` exists:
- If skills exist: List them and note they're available
- If no skills: Suggest running `/stackgen:analyze` for full skill generation
