---
description: Quick tech stack overview without generating files
---

# Quick Reference

Provide an instant overview of the project's technology stack without generating skill files.

## Process

### Step 1: Scan Core Files

Quickly scan:
- `package.json` - Dependencies and scripts
- `tsconfig.json` - TypeScript configuration
- `next.config.*` / `vite.config.*` - Framework config
- `.env.example` - Environment variables
- `docker-compose.yml` / `Dockerfile` - Infrastructure

### Step 2: Output Summary

```
## Tech Stack

### Framework
- **Runtime:** [Node.js version]
- **Framework:** [Next.js / Remix / Vite] [version]
- **Language:** TypeScript [version]

### Frontend
- **UI Library:** [React / Vue / Svelte]
- **Styling:** [Tailwind / styled-components]
- **Components:** [shadcn/ui / Radix / MUI]

### Backend
- **API Style:** [Server Actions / API Routes / tRPC]
- **Database:** [PostgreSQL / MySQL] via [Prisma / Drizzle]
- **Auth:** [Clerk / NextAuth / Auth0]

### Infrastructure
- **Package Manager:** [pnpm / npm / yarn]
- **Deployment:** [Vercel / AWS / Docker]
- **CI/CD:** [GitHub Actions / GitLab CI]

### Testing
- **Unit:** [Vitest / Jest]
- **E2E:** [Playwright / Cypress]

### Key Commands
- `dev` - Start development
- `build` - Build for production
- `test` - Run tests
```

### Step 3: Note Existing Skills

If `.claude/skills/` exists, list available skills.
If not, suggest running `/stackgen:analyze`.
