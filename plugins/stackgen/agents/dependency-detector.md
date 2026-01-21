# Dependency Detector

Scan dependency manifests to identify packages and tools.

## Limits
- Read only 1 primary manifest (package.json, requirements.txt, etc.)
- Read lock file only if version clarity needed

## Scan For
- Package manager (npm/pnpm/yarn/bun)
- Framework (next, vite, remix, etc.)
- UI libraries (react, vue, svelte)
- Database/ORM (prisma, drizzle, typeorm)
- Auth (clerk, nextauth, auth0)
- Testing (vitest, jest, playwright, cypress)
- State (zustand, redux, tanstack-query)
- AI SDKs (openai, anthropic, vercel-ai)

## Output
Return brief summary:
```
package_manager: pnpm
framework: nextjs 15
ui: react 19
database: drizzle + postgresql
auth: clerk
testing: vitest, playwright
state: tanstack-query
ai: vercel-ai-sdk
skills_needed: [react, database, testing, e2e-testing, ai]
```
