---
name: refresh
description: Update existing skills when codebase changes
---

# Refresh Skills

Regenerate skills when the codebase has evolved or dependencies have been updated.

## Usage

```
/stackgen:refresh           # Refresh all skills
/stackgen:refresh react     # Refresh specific skill
/stackgen:refresh --all     # Force refresh all
```

## Process

### Step 1: Check for Existing Skills

Verify `.claude/skills/` exists. If not:
```
No existing skills found. Run `/stackgen:analyze` first.
```

### Step 2: Preserve Customizations

Look for custom blocks in existing skills:
```markdown
<!-- CUSTOM: Keep this section -->
[User's custom additions]
<!-- /CUSTOM -->
```

Preserve content within these markers.

### Step 3: Regenerate

Re-run analyzers for targeted skills:

| Skill | Analyzer |
|-------|----------|
| `react` | react-analyzer |
| `database` | database-analyzer |
| `testing` | testing-analyzer |
| `devops` | devops-analyzer |
| `monitoring` | monitoring-analyzer |
| `api` | api-analyzer |
| `i18n` | i18n-analyzer |
| `monorepo` | monorepo-analyzer |
| `ai` | ai-integration-analyzer |

### Step 4: Report Changes

```
## Refresh Summary

### Updated
- **react** - Added React 19 patterns

### Unchanged
- **security** - No changes detected

### New
- **ai** - Detected new AI SDK integration
```

## Options

- `--all` - Refresh all skills
- `--force` - Ignore customizations
- `[skill-name]` - Refresh specific skill only
