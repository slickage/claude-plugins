---
description: Full codebase analysis and skill generation
---

# Analyze and Generate Skills

Analyze the current codebase to detect its complete technology stack, then generate tailored Claude Code skills.

## Phase 1: Tech Stack Detection

**Spawn a single stack-detector agent** using the Task tool with `model: haiku`:

```
subagent_type: stackgen:stack-detector
model: haiku
prompt: Analyze the codebase and return a comprehensive tech stack JSON including technologies, suggested_analyzers, and context for each domain.
```

The detector returns JSON with:
- `technologies` - All detected tech
- `suggested_analyzers` - Which analyzers to spawn
- `context` - Pre-analyzed findings for each domain (to pass to analyzers)

## Phase 2: Conditional Analyzer Spawning

Based on `suggested_analyzers` from the detector, spawn **ONLY the relevant analyzers in parallel**.

### Tech Detection Gating

| Condition | Spawn Analyzer |
|-----------|----------------|
| Always | `security-analyzer`, `architecture-analyzer`, `code-quality-analyzer`, `performance-analyzer` |
| React/Vue/Angular detected | `frontend-analyzer` |
| Server Actions/Express/API routes | `backend-analyzer` |
| PostgreSQL/MongoDB/Prisma/Drizzle | `database-analyzer` |
| Vitest/Jest/Playwright detected | `testing-analyzer` |
| Docker/GitHub Actions/Vercel | `devops-analyzer` |
| Sentry/logging libs detected | `monitoring-analyzer` |

### Context Passing

Pass the detector's context to each analyzer in its prompt:

```
subagent_type: stackgen:[analyzer-name]
prompt: |
  <detector-context>
  ${context.[domain]}
  Key files: ${context.[domain].files}
  </detector-context>

  Analyze the codebase and generate .claude/skills/[skill-name]/SKILL.md
```

**Launch all applicable analyzers in a SINGLE message** for maximum parallelism.

## Phase 3: Summary

After all agents complete, provide:

1. **Tech Stack Overview** - Summary from stack-detector
2. **Generated Skills** - List all skills created in `.claude/skills/`
3. **Usage Guide** - How to reference these skills
4. **Recommendations** - Any issues or suggestions

## Execution Flow

```
┌─────────────────────────────────────────────────────────┐
│                    PHASE 1                              │
│              stack-detector (Haiku)                     │
│   Dependencies + Configs + Structure + Patterns         │
└────────────────────────┬────────────────────────────────┘
                         │
                   Detection JSON
                   + Context per domain
                         │
┌─────────────────────────────────────────────────────────┐
│              PHASE 2 (Parallel, Gated)                  │
├─────────┬─────────┬─────────┬─────────┬─────────┬───────┤
│security │frontend │backend  │database │testing  │ ...   │
│(always) │(if React)│(if API)│(if DB) │(if test)│       │
└─────────┴─────────┴─────────┴─────────┴─────────┴───────┘
     │         │         │         │         │
     │    Each analyzer receives context from detector
     │
                    PHASE 3 (Summary)
```

## Agent Reference

### Core Analyzers (Always Run)
- `stackgen:security-analyzer` → .claude/skills/security/SKILL.md
- `stackgen:architecture-analyzer` → .claude/skills/architecture/SKILL.md
- `stackgen:code-quality-analyzer` → .claude/skills/code-quality/SKILL.md
- `stackgen:performance-analyzer` → .claude/skills/performance/SKILL.md

### Conditional Analyzers
- `stackgen:frontend-analyzer` → .claude/skills/frontend/SKILL.md
- `stackgen:backend-analyzer` → .claude/skills/backend/SKILL.md
- `stackgen:database-analyzer` → .claude/skills/database/SKILL.md
- `stackgen:testing-analyzer` → .claude/skills/testing/SKILL.md
- `stackgen:devops-analyzer` → .claude/skills/devops/SKILL.md
- `stackgen:monitoring-analyzer` → .claude/skills/monitoring/SKILL.md

## Output Structure

```
.claude/skills/
├── security/SKILL.md
├── architecture/SKILL.md
├── code-quality/SKILL.md
├── performance/SKILL.md
├── frontend/SKILL.md (if detected)
├── backend/SKILL.md (if detected)
├── database/SKILL.md (if detected)
├── testing/SKILL.md (if detected)
├── devops/SKILL.md (if detected)
└── monitoring/SKILL.md (if detected)
```
