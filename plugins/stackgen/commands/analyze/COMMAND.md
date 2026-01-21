---
name: analyze
description: Full codebase analysis and skill generation
---

# Analyze and Generate Skills

Analyze the current codebase to detect its complete technology stack, then generate tailored Claude Code skills that provide project-specific guidance.

## Process

### Step 1: Tech Stack Detection

Analyze the codebase to identify all technologies:

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

**Always Generate (Core):**
- `security` - Security best practices
- `performance` - Performance optimization
- `architecture` - Code structure and conventions
- `dependency-management` - Package management
- `code-quality` - Linting, typing, formatting

**Conditionally Generate:**
- `react` - If React detected
- `database` - If database/ORM detected
- `testing` - If test framework detected
- `e2e-testing` - If Playwright/Cypress detected
- `api` - If API routes detected
- `devops` - If Docker/CI-CD detected
- `monitoring` - If Sentry/logging detected
- `i18n` - If i18n libraries detected
- `monorepo` - If Turborepo/Nx detected
- `ai` - If AI SDKs detected

### Step 3: Generate Skills

For each skill, create `.claude/skills/[skill-name]/SKILL.md` with:
- YAML frontmatter (name, description)
- Project-specific instructions
- Code examples from the actual codebase
- Do's and Don'ts
- Key file references

### Step 4: Summary

Provide:
- Complete tech stack inventory
- List of generated skills
- How to use each skill
- Customization suggestions

## Output Structure

```
.claude/skills/
├── security/SKILL.md
├── performance/SKILL.md
├── architecture/SKILL.md
├── code-quality/SKILL.md
├── react/SKILL.md (if detected)
├── database/SKILL.md (if detected)
└── ...
```
