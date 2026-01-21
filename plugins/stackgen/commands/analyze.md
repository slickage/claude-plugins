---
description: Full codebase analysis and skill generation
---

# Analyze and Generate Skills

Analyze the current codebase to detect its complete technology stack, then generate tailored Claude Code skills.

## Phase 1: Tech Stack Detection

**Spawn the tech-stack-detector agent** to identify all technologies in use.

Use the Task tool:
```
subagent_type: stackgen:tech-stack-detector
prompt: Analyze this codebase and return a complete tech stack report. Include all frameworks, libraries, tools, and a list of skills_to_generate based on what you find.
```

Wait for the tech stack report before proceeding.

## Phase 2: Run Analyzers in Parallel

Based on the tech stack detection, spawn ALL relevant analyzer agents **in parallel** using multiple Task tool calls in a single message.

### Core Analyzers (Always Run)

Spawn these 5 agents in parallel:

| Agent | Task Prompt |
|-------|-------------|
| `stackgen:security-analyzer` | Analyze security patterns and generate .claude/skills/security/SKILL.md |
| `stackgen:performance-analyzer` | Analyze performance patterns and generate .claude/skills/performance/SKILL.md |
| `stackgen:architecture-analyzer` | Analyze architecture patterns and generate .claude/skills/architecture/SKILL.md |
| `stackgen:dependency-analyzer` | Analyze dependency management and generate .claude/skills/dependency-management/SKILL.md |
| `stackgen:code-quality-analyzer` | Analyze code quality patterns and generate .claude/skills/code-quality/SKILL.md |

### Conditional Analyzers (Based on Detection)

Spawn these agents **only if detected** in the tech stack:

| Condition | Agent | Task Prompt |
|-----------|-------|-------------|
| React detected | `stackgen:react-analyzer` | Analyze React patterns and generate .claude/skills/react/SKILL.md |
| Database/ORM detected | `stackgen:database-analyzer` | Analyze database patterns and generate .claude/skills/database/SKILL.md |
| Test framework detected | `stackgen:testing-analyzer` | Analyze testing patterns and generate .claude/skills/testing/SKILL.md |
| Playwright/Cypress detected | `stackgen:e2e-testing-analyzer` | Analyze E2E testing patterns and generate .claude/skills/e2e-testing/SKILL.md |
| Frontend framework detected | `stackgen:frontend-analyzer` | Analyze frontend patterns and generate .claude/skills/frontend/SKILL.md |
| Backend patterns detected | `stackgen:backend-analyzer` | Analyze backend patterns and generate .claude/skills/backend/SKILL.md |
| API routes detected | `stackgen:api-analyzer` | Analyze API patterns and generate .claude/skills/api/SKILL.md |
| Docker/CI-CD detected | `stackgen:devops-analyzer` | Analyze DevOps patterns and generate .claude/skills/devops/SKILL.md |
| Sentry/logging detected | `stackgen:monitoring-analyzer` | Analyze monitoring patterns and generate .claude/skills/monitoring/SKILL.md |
| i18n libraries detected | `stackgen:i18n-analyzer` | Analyze i18n patterns and generate .claude/skills/i18n/SKILL.md |
| Monorepo setup detected | `stackgen:monorepo-analyzer` | Analyze monorepo patterns and generate .claude/skills/monorepo/SKILL.md |
| AI SDKs detected | `stackgen:ai-integration-analyzer` | Analyze AI integration patterns and generate .claude/skills/ai/SKILL.md |

**Important:** Launch all applicable agents in a SINGLE message with multiple Task tool calls for maximum parallelism.

## Phase 3: Summary

After all agents complete, provide:

1. **Tech Stack Overview** - Summary from tech-stack-detector
2. **Generated Skills** - List all skills created in `.claude/skills/`
3. **Usage Guide** - How to reference these skills
4. **Recommendations** - Any issues or suggestions from the analyzers

## Output Structure

```
.claude/skills/
├── security/SKILL.md
├── performance/SKILL.md
├── architecture/SKILL.md
├── dependency-management/SKILL.md
├── code-quality/SKILL.md
├── react/SKILL.md (if detected)
├── database/SKILL.md (if detected)
├── testing/SKILL.md (if detected)
├── e2e-testing/SKILL.md (if detected)
├── frontend/SKILL.md (if detected)
├── backend/SKILL.md (if detected)
├── api/SKILL.md (if detected)
├── devops/SKILL.md (if detected)
├── monitoring/SKILL.md (if detected)
├── i18n/SKILL.md (if detected)
├── monorepo/SKILL.md (if detected)
└── ai/SKILL.md (if detected)
```
