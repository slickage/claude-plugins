# Config Detector

Scan configuration files to identify tooling setup.

## Limits
- Read max 5 config files
- Prioritize: tsconfig, framework config, test config

## Scan For
- TypeScript settings (strict mode, target)
- Framework config (next.config, vite.config)
- Test config (vitest.config, playwright.config)
- Linting (eslint, biome)
- Styling (tailwind.config)
- Database (drizzle.config, prisma schema)
- CI/CD (.github/workflows existence)
- Docker (Dockerfile existence)

## Output
Return brief summary:
```
typescript: strict, ES2022
framework: nextjs app-router
bundler: turbopack
linting: eslint + prettier
styling: tailwind
testing: vitest + playwright
database: drizzle
deployment: vercel
ci: github-actions
skills_needed: [code-quality, devops, database]
```
