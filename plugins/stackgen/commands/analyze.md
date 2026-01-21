---
description: Full codebase analysis and skill generation
---

# Analyze and Generate Skills

Analyze the current codebase to detect its complete technology stack, then generate tailored Claude Code skills.

## Phase 1: Parallel Tech Stack Detection

**Spawn all 4 detector agents in parallel** using multiple Task tool calls in a SINGLE message:

| Agent | Purpose |
|-------|---------|
| `stackgen:dependency-detector` | Scan package.json, requirements.txt, Cargo.toml, etc. |
| `stackgen:config-detector` | Scan tsconfig, eslint, framework configs, etc. |
| `stackgen:structure-detector` | Analyze directory structure and organization |
| `stackgen:pattern-detector` | Sample source files for coding patterns |

Each agent returns JSON with `detected_technologies` and `suggested_skills` arrays.

**Task prompts:**
```
dependency-detector: Scan all dependency manifests and return detected technologies as JSON.
config-detector: Scan all configuration files and return detected technologies as JSON.
structure-detector: Analyze directory structure and return detected patterns as JSON.
pattern-detector: Sample source files and return detected coding patterns as JSON.
```

## Phase 2: Merge Detection Results

After all 4 detectors complete, merge their results:

1. Combine all `detected_technologies` arrays (deduplicate)
2. Combine all `suggested_skills` arrays (deduplicate)
3. Create unified tech stack summary

## Phase 3: Run Analyzers in Parallel

Based on merged detection results, spawn ALL relevant analyzer agents **in parallel** using multiple Task tool calls in a SINGLE message.

### Core Analyzers (Always Run)

| Agent | Task Prompt |
|-------|-------------|
| `stackgen:security-analyzer` | Analyze security patterns and generate .claude/skills/security/SKILL.md |
| `stackgen:performance-analyzer` | Analyze performance patterns and generate .claude/skills/performance/SKILL.md |
| `stackgen:architecture-analyzer` | Analyze architecture patterns and generate .claude/skills/architecture/SKILL.md |
| `stackgen:dependency-analyzer` | Analyze dependency management and generate .claude/skills/dependency-management/SKILL.md |
| `stackgen:code-quality-analyzer` | Analyze code quality patterns and generate .claude/skills/code-quality/SKILL.md |

### Conditional Analyzers (Based on Detection)

| Condition | Agent | Skill Generated |
|-----------|-------|-----------------|
| React detected | `stackgen:react-analyzer` | .claude/skills/react/SKILL.md |
| Database/ORM detected | `stackgen:database-analyzer` | .claude/skills/database/SKILL.md |
| Test framework detected | `stackgen:testing-analyzer` | .claude/skills/testing/SKILL.md |
| Playwright/Cypress detected | `stackgen:e2e-testing-analyzer` | .claude/skills/e2e-testing/SKILL.md |
| Frontend framework detected | `stackgen:frontend-analyzer` | .claude/skills/frontend/SKILL.md |
| Backend patterns detected | `stackgen:backend-analyzer` | .claude/skills/backend/SKILL.md |
| API routes detected | `stackgen:api-analyzer` | .claude/skills/api/SKILL.md |
| Docker/CI-CD detected | `stackgen:devops-analyzer` | .claude/skills/devops/SKILL.md |
| Sentry/logging detected | `stackgen:monitoring-analyzer` | .claude/skills/monitoring/SKILL.md |
| i18n libraries detected | `stackgen:i18n-analyzer` | .claude/skills/i18n/SKILL.md |
| Monorepo setup detected | `stackgen:monorepo-analyzer` | .claude/skills/monorepo/SKILL.md |
| AI SDKs detected | `stackgen:ai-integration-analyzer` | .claude/skills/ai/SKILL.md |

**Launch all applicable analyzers in a SINGLE message** for maximum parallelism.

## Phase 4: Summary

After all agents complete, provide:

1. **Tech Stack Overview** - Merged summary from all detectors
2. **Generated Skills** - List all skills created in `.claude/skills/`
3. **Usage Guide** - How to reference these skills
4. **Recommendations** - Any issues or suggestions from the analyzers

## Execution Flow

```
┌─────────────────────────────────────────────────────────┐
│                    PHASE 1 (Parallel)                   │
├──────────────┬──────────────┬─────────────┬─────────────┤
│ dependency-  │ config-      │ structure-  │ pattern-    │
│ detector     │ detector     │ detector    │ detector    │
└──────┬───────┴──────┬───────┴──────┬──────┴──────┬──────┘
       │              │              │             │
       └──────────────┴──────────────┴─────────────┘
                          │
                    PHASE 2 (Merge)
                          │
       ┌──────────────────┴──────────────────┐
       │         Unified Tech Stack          │
       └──────────────────┬──────────────────┘
                          │
┌─────────────────────────────────────────────────────────┐
│                    PHASE 3 (Parallel)                   │
├─────────┬─────────┬─────────┬─────────┬─────────┬───────┤
│security │perform- │architec-│ react   │database │ ...   │
│analyzer │ance     │ture     │analyzer │analyzer │       │
└─────────┴─────────┴─────────┴─────────┴─────────┴───────┘
                          │
                    PHASE 4 (Summary)
```

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
└── ... (other conditional skills)
```
