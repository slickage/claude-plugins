---
description: Update existing skills when codebase changes
---

# Refresh Skills

Regenerate skills when the codebase has evolved or dependencies have been updated.

## Usage

```
/stackgen:refresh           # Refresh all skills
/stackgen:refresh frontend  # Refresh specific skill
/stackgen:refresh frontend,backend # Refresh multiple skills
```

## Phase 1: Validate Existing Skills

First, check if `.claude/skills/` exists. If not:
```
No existing skills found. Run `/stackgen:analyze` first.
```

List all existing skills in `.claude/skills/`.

## Phase 2: Run Stack Detector

**Spawn stack-detector** to get current context for analyzers:

```
subagent_type: stackgen:stack-detector
model: haiku
prompt: Analyze the codebase and return context for the following domains: [list of skills to refresh]
```

## Phase 3: Identify Skills to Refresh

**If specific skills requested:** Only refresh those skills.

**If refreshing all:** Get the list of existing skill folders.

## Phase 4: Preserve Customizations

Before regenerating, scan each skill file for custom blocks:

```markdown
<!-- CUSTOM: description -->
[User's custom additions]
<!-- /CUSTOM -->
```

Store these blocks to re-inject after regeneration.

## Phase 5: Spawn Analyzer Agents with Context

**Spawn the relevant analyzer agents in parallel**, passing context from the detector.

Map skills to agents:

| Skill | Agent |
|-------|-------|
| security | `stackgen:security-analyzer` |
| performance | `stackgen:performance-analyzer` |
| architecture | `stackgen:architecture-analyzer` |
| code-quality | `stackgen:code-quality-analyzer` |
| frontend | `stackgen:frontend-analyzer` |
| backend | `stackgen:backend-analyzer` |
| database | `stackgen:database-analyzer` |
| testing | `stackgen:testing-analyzer` |
| devops | `stackgen:devops-analyzer` |
| monitoring | `stackgen:monitoring-analyzer` |

Pass context to each analyzer:

```
subagent_type: stackgen:[analyzer-name]
prompt: |
  <detector-context>
  ${context.[domain]}
  </detector-context>

  Re-analyze the codebase and regenerate .claude/skills/[skill-name]/SKILL.md with updated patterns.
```

**Launch all applicable agents in a SINGLE message** for maximum parallelism.

## Phase 6: Re-inject Customizations

After each skill is regenerated, append any preserved custom blocks.

## Phase 7: Report Changes

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
| frontend | Added React 19 patterns, updated Server Components guidance |
| backend | Added new endpoint patterns, updated error handling |

### Preserved Customizations
- frontend: 1 custom block preserved
- backend: 2 custom blocks preserved

### Recommendations
- Consider running `/stackgen:check` to verify all skills are current
```
