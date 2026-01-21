---
description: Update existing skills when codebase changes
---

# Refresh Skills

Regenerate skills when the codebase has evolved or dependencies have been updated.

## Usage

```
/stackgen:refresh           # Refresh all skills
/stackgen:refresh react     # Refresh specific skill
/stackgen:refresh react,api # Refresh multiple skills
```

## Phase 1: Validate Existing Skills

First, check if `.claude/skills/` exists. If not:
```
No existing skills found. Run `/stackgen:analyze` first.
```

List all existing skills in `.claude/skills/`.

## Phase 2: Identify Skills to Refresh

**If specific skills requested:** Only refresh those skills.

**If refreshing all:** Get the list of existing skill folders.

## Phase 3: Preserve Customizations

Before regenerating, scan each skill file for custom blocks:

```markdown
<!-- CUSTOM: description -->
[User's custom additions]
<!-- /CUSTOM -->
```

Store these blocks to re-inject after regeneration.

## Phase 4: Spawn Analyzer Agents

**Spawn the relevant analyzer agents in parallel** based on which skills need refreshing.

Map skills to agents:

| Skill | Agent |
|-------|-------|
| security | `stackgen:security-analyzer` |
| performance | `stackgen:performance-analyzer` |
| architecture | `stackgen:architecture-analyzer` |
| dependency-management | `stackgen:dependency-analyzer` |
| code-quality | `stackgen:code-quality-analyzer` |
| react | `stackgen:react-analyzer` |
| database | `stackgen:database-analyzer` |
| testing | `stackgen:testing-analyzer` |
| e2e-testing | `stackgen:e2e-testing-analyzer` |
| frontend | `stackgen:frontend-analyzer` |
| backend | `stackgen:backend-analyzer` |
| api | `stackgen:api-analyzer` |
| devops | `stackgen:devops-analyzer` |
| monitoring | `stackgen:monitoring-analyzer` |
| i18n | `stackgen:i18n-analyzer` |
| monorepo | `stackgen:monorepo-analyzer` |
| ai | `stackgen:ai-integration-analyzer` |

Use the Task tool for each skill to refresh:
```
subagent_type: stackgen:[analyzer-name]
prompt: Re-analyze the codebase and regenerate .claude/skills/[skill-name]/SKILL.md with updated patterns and best practices.
```

**Launch all applicable agents in a SINGLE message** for maximum parallelism.

## Phase 5: Re-inject Customizations

After each skill is regenerated, append any preserved custom blocks.

## Phase 6: Report Changes

For each refreshed skill, report:
- What changed (new patterns detected, removed patterns, updated versions)
- Any preserved customizations
- Recommendations

Example output:
```
## Refresh Complete

### Updated Skills

| Skill | Changes |
|-------|---------|
| react | Added React 19 patterns, updated Server Components guidance |
| api | Added new endpoint patterns, updated error handling |

### Preserved Customizations
- react: 1 custom block preserved
- api: 2 custom blocks preserved

### Recommendations
- Consider running `/stackgen:check` to verify all skills are current
```
