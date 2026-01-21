---
name: analyze
description: Analyzes the codebase to detect tech stack and generates tailored Claude Code skills. Use when setting up a new project, onboarding to a codebase, or wanting project-specific AI assistance. Generates skills for security, performance, architecture, and detected technologies.
---

# Analyze and Generate Skills

Analyze the current codebase to detect its complete technology stack, then generate tailored Claude Code skills that provide project-specific guidance.

## Process Overview

### Step 1: Tech Stack Detection

First, thoroughly analyze the codebase to identify all technologies:

1. **Read dependency files** (package.json, requirements.txt, etc.)
   - Extract all dependencies with versions
   - Note dev vs production dependencies
   - Identify package manager (npm, pnpm, yarn)

2. **Analyze configuration files**
   - tsconfig.json - TypeScript settings
   - eslint.config.js - Linting rules
   - Framework configs (next.config.ts, vite.config.ts)
   - ORM configs (drizzle.config.ts, prisma schema)
   - Test configs (vitest.config.ts)

3. **Examine project structure**
   - Directory organization patterns
   - Feature vs layer-based architecture
   - Component organization

4. **Sample code patterns**
   - State management approach
   - Styling methodology
   - API patterns
   - Auth implementation

### Step 2: Determine Skills to Generate

Based on detection, generate these skills:

**Always Generate (Core):**
- `security` - Security best practices for the detected stack
- `performance` - Performance optimization patterns
- `architecture` - Code structure and conventions
- `dependency-management` - Package management guidelines
- `code-quality` - Linting, typing, formatting standards

**Conditionally Generate (based on detection):**
- `react` - If React detected (react-analyzer)
- `nextjs` - If Next.js detected
- `vue` / `angular` / `svelte` - If detected
- `database` - If database/ORM detected (database-analyzer)
- `testing` - If test framework detected (testing-analyzer)
- `e2e-testing` - If Playwright/Cypress detected (e2e-testing-analyzer)
- `frontend` - If frontend framework detected (frontend-analyzer)
- `backend` - If backend patterns detected (backend-analyzer)
- `api` - If API routes/GraphQL/tRPC detected (api-analyzer)
- `auth` - If auth provider detected (Clerk, Auth0, etc.)
- `state-management` - If TanStack Query, Redux, Zustand detected
- `styling` - If Tailwind, styled-components detected
- `realtime` - If WebSocket/real-time detected
- `pwa` - If PWA config detected
- `ai` - If AI SDKs detected (ai-integration-analyzer)
- `devops` - If Docker/CI-CD detected (devops-analyzer)
- `monitoring` - If Sentry/logging detected (monitoring-analyzer)
- `i18n` - If i18n libraries detected (i18n-analyzer)
- `monorepo` - If Turborepo/Nx/workspaces detected (monorepo-analyzer)

### Step 3: Generate Skills

For each skill to generate:

1. **Create directory**: `.claude/skills/[skill-name]/`

2. **Create SKILL.md** with:
   - YAML frontmatter (name, description)
   - Project-specific instructions
   - Code examples from the actual codebase
   - Do's and Don'ts
   - File references

### Step 4: Summary

After generation, provide:
- Complete tech stack inventory
- List of generated skills
- How to use each skill
- Suggestions for customization

## SKILL.md Format

Each generated skill must follow this format:

```markdown
---
name: skill-name
description: Clear description of when Claude should apply this skill. Include trigger words and specific capabilities.
---

# Skill Title

## Overview
[What this skill covers]

## Patterns

### Pattern Name
```language
// Code example from this codebase
```

## Do's
- [Specific guidance]

## Don'ts
- [Anti-patterns to avoid]

## Key Files
- `path/to/file.ts` - [Purpose]
```

## Execution Instructions

When this skill is invoked:

1. **Create output directory** if it doesn't exist:
   ```
   .claude/skills/
   ```

2. **Detect tech stack** by reading:
   - `package.json`
   - `tsconfig.json`
   - Framework config files
   - Sample source files

3. **For each skill to generate**, create a subdirectory and SKILL.md file

4. **Reference actual code** from the codebase in examples

5. **Be specific** - generic advice is less useful than project-specific patterns

## Example Output Structure

```
.claude/skills/
├── security/
│   └── SKILL.md
├── performance/
│   └── SKILL.md
├── architecture/
│   └── SKILL.md
├── dependency-management/
│   └── SKILL.md
├── code-quality/
│   └── SKILL.md
├── react/
│   └── SKILL.md
├── nextjs/
│   └── SKILL.md
├── database/
│   └── SKILL.md
├── testing/
│   └── SKILL.md
├── e2e-testing/
│   └── SKILL.md
├── api/
│   └── SKILL.md
├── devops/
│   └── SKILL.md
├── monitoring/
│   └── SKILL.md
├── i18n/
│   └── SKILL.md
├── monorepo/
│   └── SKILL.md
├── ai/
│   └── SKILL.md
└── [other detected skills]/
    └── SKILL.md
```

## Analyzer Agents Reference

The following specialized agents are used for analysis:

| Agent | Purpose | Activation |
|-------|---------|------------|
| tech-stack-detector | Detects all technologies | Always |
| code-quality-analyzer | Linting, typing, formatting | Always |
| security-analyzer | Security patterns | Always |
| performance-analyzer | Performance optimization | Always |
| architecture-analyzer | Code structure | Always |
| dependency-analyzer | Package management | Always |
| react-analyzer | React patterns | React detected |
| database-analyzer | ORM/database patterns | Database detected |
| testing-analyzer | Unit/integration tests | Test framework detected |
| e2e-testing-analyzer | E2E test patterns | Playwright/Cypress detected |
| frontend-analyzer | Frontend framework patterns | Frontend detected |
| backend-analyzer | Backend/API patterns | Backend detected |
| api-analyzer | API design patterns | API routes detected |
| ai-integration-analyzer | LLM/AI patterns | AI SDK detected |
| devops-analyzer | CI/CD, Docker patterns | Docker/CI detected |
| monitoring-analyzer | Logging, error tracking | Sentry/logging detected |
| i18n-analyzer | Internationalization | i18n library detected |
| monorepo-analyzer | Workspace patterns | Turborepo/Nx detected |

## Tips for Quality Skills

1. **Be specific** - Reference actual file paths and code patterns
2. **Include versions** - Best practices vary by version
3. **Show real examples** - Pull from the actual codebase
4. **Explain why** - Not just what, but why certain patterns exist
5. **Keep focused** - One skill = one domain
6. **Use clear triggers** - Help Claude know when to apply each skill
