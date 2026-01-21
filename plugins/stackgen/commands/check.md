---
description: Audit skills for outdated or missing patterns
---

# Check Skills

Audit existing skills to identify outdated patterns, missing skills, or orphaned skills.

## Phase 1: Inventory Existing Skills

Scan `.claude/skills/` and list all current skills with their metadata.

If no skills exist:
```
No skills found. Run `/stackgen:analyze` to generate skills.
```

## Phase 2: Detect Current Tech Stack

**Spawn the tech-stack-detector agent** to get the current state of the codebase.

Use the Task tool:
```
subagent_type: stackgen:tech-stack-detector
prompt: Analyze this codebase and return a complete tech stack report with skills_to_generate list. Focus on identifying any NEW technologies that may have been added recently.
```

## Phase 3: Compare and Analyze

Compare existing skills against the detected tech stack:

### Categories

1. **Up-to-date Skills** - Skills that match current tech stack
2. **Outdated Skills** - Skills that may need updating (version changes, deprecated patterns)
3. **Missing Skills** - Technologies detected but no skill exists
4. **Orphaned Skills** - Skills for technologies no longer in use

## Phase 4: Generate Report

```
## Skill Audit Report

### Summary
- Total Skills: X
- Up-to-date: X
- Need Updates: X
- Missing: X
- Orphaned: X

### Up-to-date Skills
| Skill | Status |
|-------|--------|
| security | Current |
| react | Current |

### Skills Needing Updates
| Skill | Reason |
|-------|--------|
| database | Drizzle updated from 0.29 to 0.35 |
| testing | New test patterns detected |

### Missing Skills
| Technology | Recommended Skill |
|------------|-------------------|
| Playwright | e2e-testing |
| Sentry | monitoring |

### Orphaned Skills
| Skill | Reason |
|-------|--------|
| vue | Vue no longer detected in codebase |

### Recommendations

**High Priority:**
1. Run `/stackgen:refresh database,testing` to update outdated skills
2. Run `/stackgen:analyze` to generate missing skills

**Optional:**
- Remove orphaned skills: `.claude/skills/vue/`
```

## Phase 5: Offer Actions

Based on the audit, offer to:
1. Refresh outdated skills
2. Generate missing skills
3. Remove orphaned skills

Ask user which actions to take before proceeding.
