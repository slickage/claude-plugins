---
name: refresh
description: Update existing generated skills when the codebase has changed or dependencies have been updated
---

# Refresh Skills

## Purpose

Regenerate specific skills or all skills when the codebase has evolved, dependencies have been updated, or patterns have changed. Preserves any customizations marked in skill files.

## Invocation

```
/stackgen:refresh
/stackgen:refresh [skill-name]
/stackgen:refresh --all
```

## Process

### Step 1: Check for Existing Skills

Verify that `.claude/skills/` directory exists. If not, inform the user:

```
No existing skills found. Run `/stackgen:analyze` first to generate skills.
```

### Step 2: Detect Changes (if refreshing specific skill)

For the targeted skill(s), re-run the corresponding analyzer agent(s) to:

1. Detect any new patterns or configurations
2. Identify updated versions of frameworks/libraries
3. Find new files that match the skill's domain

### Step 3: Preserve Customizations

Before regenerating, check for customization markers in existing skills:

```markdown
<!-- CUSTOM: Keep this section -->
[User's custom additions]
<!-- /CUSTOM -->
```

Preserve any content within `<!-- CUSTOM -->` blocks.

### Step 4: Regenerate Skills

Re-run the appropriate analyzer agents:

| Skill | Analyzer Agent |
|-------|----------------|
| `react` | react-analyzer |
| `database` | database-analyzer |
| `testing` | testing-analyzer |
| `frontend` | frontend-analyzer |
| `backend` | backend-analyzer |
| `security` | security-analyzer |
| `performance` | performance-analyzer |
| `architecture` | architecture-analyzer |
| `code-quality` | code-quality-analyzer |
| `dependency-management` | dependency-analyzer |
| `devops` | devops-analyzer |
| `monitoring` | monitoring-analyzer |
| `api` | api-analyzer |
| `e2e-testing` | e2e-testing-analyzer |
| `i18n` | i18n-analyzer |
| `monorepo` | monorepo-analyzer |
| `ai` | ai-integration-analyzer |

### Step 5: Report Changes

Provide a summary of what changed:

```
## Skills Refresh Summary

### Updated Skills
- **react** - Added React 19 patterns, updated Server Components guidance
- **database** - Added new migration patterns from recent changes

### Unchanged Skills
- **security** - No changes detected
- **testing** - No changes detected

### New Skills Generated
- **ai** - Detected new AI SDK integration

### Removed Skills
- **graphql** - GraphQL dependencies removed from project
```

## Options

### Refresh All Skills
```
/stackgen:refresh --all
```

Re-runs all analyzer agents and regenerates all skills.

### Refresh Specific Skill
```
/stackgen:refresh react
```

Only re-runs the react-analyzer and updates the react skill.

### Force Refresh (Ignore Customizations)
```
/stackgen:refresh --force
```

Regenerates skills without preserving customizations. Use with caution.

## Output Guidelines

- Always show what changed vs. what stayed the same
- Warn before overwriting customizations
- Suggest reviewing generated skills after refresh
- Note if new technologies were detected that warrant new skills
