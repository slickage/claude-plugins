---
description: Update existing skills when codebase changes
---

# Refresh Skills

Regenerate skills when the codebase has evolved or dependencies have been updated.

## Usage

```
/stackgen:refresh           # Refresh all skills
/stackgen:refresh react     # Refresh specific skill
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

### Step 3: Regenerate

Re-run analyzers for targeted skills and report changes.
