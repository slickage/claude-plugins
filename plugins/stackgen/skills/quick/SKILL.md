---
name: quick
description: Quickly show the detected tech stack and key patterns without running full analysis
---

# Quick Reference

## Purpose

Provide an instant overview of the project's technology stack and key conventions without generating new skill files. Useful for quick context when starting work or onboarding.

## Invocation

```
/stackgen:quick
```

## Process

### Step 1: Scan Core Configuration Files

Quickly scan these files to identify the tech stack:

- `package.json` - Dependencies and scripts
- `tsconfig.json` - TypeScript configuration
- `next.config.*` / `vite.config.*` / `nuxt.config.*` - Framework config
- `.env.example` - Environment variables (for service detection)
- `docker-compose.yml` / `Dockerfile` - Infrastructure

### Step 2: Generate Quick Summary

Output a concise summary in this format:

```
## Tech Stack

### Framework
- **Runtime:** [Node.js version if detected]
- **Framework:** [Next.js / Remix / Vite / etc.] [version]
- **Language:** TypeScript [version] / JavaScript

### Frontend
- **UI Library:** [React / Vue / Svelte] [version]
- **Styling:** [Tailwind CSS / styled-components / CSS Modules]
- **Components:** [shadcn/ui / Radix / MUI / custom]

### Backend
- **API Style:** [Server Actions / API Routes / tRPC / REST]
- **Database:** [PostgreSQL / MySQL / MongoDB] via [Prisma / Drizzle]
- **Auth:** [Clerk / NextAuth / Auth0 / custom]

### Infrastructure
- **Package Manager:** [pnpm / npm / yarn / bun]
- **Deployment:** [Vercel / AWS / Railway / Docker]
- **CI/CD:** [GitHub Actions / GitLab CI]

### Testing
- **Unit:** [Vitest / Jest]
- **E2E:** [Playwright / Cypress]

### Key Commands
- `[dev command]` - Start development
- `[build command]` - Build for production
- `[test command]` - Run tests
- `[lint command]` - Lint code
```

### Step 3: Note Existing Skills

If `.claude/skills/` exists, list the generated skills:

```
### Generated Skills Available
- `/skills/react` - React patterns
- `/skills/database` - Database patterns
- [etc.]
```

If no skills exist, suggest running `/stackgen:analyze`.

## Output Guidelines

- Keep the output **concise and scannable**
- Use **bold** for technology names
- Include **versions** where available
- Only show sections that are **relevant** to the project
- Skip empty or not-applicable sections
