---
description: Full codebase analysis and skill generation
---

# Analyze and Generate Skills

Analyze the codebase and generate tailored Claude Code skills.

## Phase 1: Parallel Detection (Use Haiku)

**Spawn all 4 detectors in parallel using `model: haiku`** for fast, cheap detection:

```
Task calls (all in ONE message):
- subagent_type: stackgen:dependency-detector, model: haiku
- subagent_type: stackgen:config-detector, model: haiku
- subagent_type: stackgen:structure-detector, model: haiku
- subagent_type: stackgen:pattern-detector, model: haiku
```

Each returns a brief summary with `skills_needed` list.

## Phase 2: Merge Results

Combine detector outputs into unified tech stack:
- Deduplicate skills_needed lists
- Create summary for context passing

## Phase 3: Run Analyzers (Pass Context)

**Pass the merged detection context to each analyzer** so they skip re-scanning.

### Core Analyzers (Always Run - 5 agents)

```
For each, include in prompt:
"Tech stack context: {merged_detection_summary}. Generate skill file."
```

| Agent | Output |
|-------|--------|
| `stackgen:security-analyzer` | .claude/skills/security/SKILL.md |
| `stackgen:performance-analyzer` | .claude/skills/performance/SKILL.md |
| `stackgen:architecture-analyzer` | .claude/skills/architecture/SKILL.md |
| `stackgen:dependency-analyzer` | .claude/skills/dependency-management/SKILL.md |
| `stackgen:code-quality-analyzer` | .claude/skills/code-quality/SKILL.md |

### Conditional Analyzers (Only if detected - up to 9 agents)

**Skip if fewer than 3 relevant files detected.**

| Condition | Agent | Output |
|-----------|-------|--------|
| React/Vue/Svelte | `stackgen:frontend-analyzer` | .claude/skills/frontend/SKILL.md |
| Server Actions/API | `stackgen:backend-analyzer` | .claude/skills/backend/SKILL.md |
| Database/ORM | `stackgen:database-analyzer` | .claude/skills/database/SKILL.md |
| Test framework | `stackgen:testing-analyzer` | .claude/skills/testing/SKILL.md |
| Docker/CI-CD | `stackgen:devops-analyzer` | .claude/skills/devops/SKILL.md |
| Sentry/logging | `stackgen:monitoring-analyzer` | .claude/skills/monitoring/SKILL.md |
| i18n library | `stackgen:i18n-analyzer` | .claude/skills/i18n/SKILL.md |
| Monorepo tools | `stackgen:monorepo-analyzer` | .claude/skills/monorepo/SKILL.md |
| AI SDKs | `stackgen:ai-integration-analyzer` | .claude/skills/ai/SKILL.md |

**Launch all applicable analyzers in a SINGLE message.**

## Phase 4: Summary

Report:
1. Tech stack overview
2. Skills generated (list paths)
3. Any skipped analyzers and why

## Execution Flow

```
PHASE 1: 4 detectors (haiku, parallel) ─────────────┐
                                                     │
PHASE 2: Merge into context ─────────────────────────┤
                                                     │
PHASE 3: 5-14 analyzers (parallel, with context) ───┤
                                                     │
PHASE 4: Summary ────────────────────────────────────┘
```

## Output

```
.claude/skills/
├── security/SKILL.md
├── performance/SKILL.md
├── architecture/SKILL.md
├── dependency-management/SKILL.md
├── code-quality/SKILL.md
└── [conditional skills based on detection]
```
