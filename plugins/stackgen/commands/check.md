---
name: check
description: Audit skills for outdated or missing patterns
---

# Check Skills

Audit existing skills to identify outdated patterns or missing skills.

## Process

### Step 1: Inventory Skills

Scan `.claude/skills/` and list:
- Skill name
- Last modified date
- Description

### Step 2: Detect Current Tech Stack

Run tech-stack-detector to get current technologies.

### Step 3: Compare

```
## Skills Audit

### Up-to-Date
| Skill | Technology | Status |
|-------|------------|--------|
| react | React 19.0 | Current |
| database | Prisma 5.x | Current |

### Need Update
| Skill | Issue |
|-------|-------|
| testing | Vitest upgraded 1.x → 2.x |

### Missing Skills
| Technology | Recommended |
|------------|-------------|
| Playwright | e2e-testing |
| Sentry | monitoring |

### Orphaned Skills
| Skill | Issue |
|-------|-------|
| graphql | No longer detected |
```

### Step 4: Recommendations

```
## Recommendations

### High Priority
1. Regenerate `testing` - Major version change

### Medium Priority
2. Generate `monitoring` - Sentry detected

### Action
Run `/stackgen:refresh --all` to update
```

## Validation Checks

1. **File References** - Do referenced files exist?
2. **Version Accuracy** - Match package.json?
3. **Pattern Validity** - Code examples still accurate?
