---
description: Audit skills for outdated or missing patterns
---

# Check Skills

Audit existing skills to identify outdated patterns or missing skills.

## Process

### Step 1: Inventory Skills

Scan `.claude/skills/` and list all skills with their status.

### Step 2: Detect Current Tech Stack

Run tech-stack-detector to get current technologies.

### Step 3: Compare and Report

Identify:
- Skills that are up-to-date
- Skills that need updates
- Missing skills for detected technologies
- Orphaned skills (no longer needed)

### Step 4: Recommendations

Provide prioritized list of actions:
- High priority updates
- New skills to generate
- Skills to remove

Suggest running `/stackgen:refresh --all` if needed.
