---
description: Update existing skills when codebase changes
---

# Refresh Skills

Regenerate skills when codebase evolves.

## Usage

```
/stackgen:refresh           # All skills
/stackgen:refresh react     # Specific skill
/stackgen:refresh react,api # Multiple skills
```

## Phase 1: Validate

Check `.claude/skills/` exists. If not:
```
No skills found. Run `/stackgen:analyze` first.
```

List existing skills.

## Phase 2: Preserve Custom Blocks

Scan skill files for:
```markdown
<!-- CUSTOM: description -->
[User content]
<!-- /CUSTOM -->
```

Store for re-injection.

## Phase 3: Run Detectors (Haiku)

Quick detection for context:
```
Task calls (ONE message, model: haiku):
- stackgen:dependency-detector
- stackgen:config-detector
```

## Phase 4: Spawn Analyzers

Map skills to analyzers:

| Skill | Agent |
|-------|-------|
| security | security-analyzer |
| performance | performance-analyzer |
| architecture | architecture-analyzer |
| dependency-management | dependency-analyzer |
| code-quality | code-quality-analyzer |
| frontend | frontend-analyzer |
| backend | backend-analyzer |
| database | database-analyzer |
| testing | testing-analyzer |
| devops | devops-analyzer |
| monitoring | monitoring-analyzer |
| i18n | i18n-analyzer |
| monorepo | monorepo-analyzer |
| ai | ai-integration-analyzer |

**Pass detection context to each analyzer.**

Launch in parallel.

## Phase 5: Re-inject Custom Blocks

Append preserved custom blocks to regenerated skills.

## Phase 6: Report

```
## Refresh Complete

**Updated:** frontend, backend, database
**Preserved:** 2 custom blocks

**Changes:**
- frontend: Added React 19 patterns
- backend: Updated Server Actions patterns
```
