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

## Phase 2: Tech Stack Detection

**Spawn a single stack-detector agent** to get the current state of the codebase:

```
subagent_type: stackgen:stack-detector
model: haiku
prompt: Analyze the codebase and return current tech stack. Include suggested_analyzers to compare against existing skills.
```

## Phase 3: Compare and Audit

Compare detector results against existing skills:

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

### Detection Summary
| Category | Key Findings |
|----------|--------------|
| Frontend | React 19.0.0, Next 15.1.0 (updated) |
| Backend | Server Actions, new API patterns |
| Database | Drizzle 0.35 (version change) |
| Testing | Playwright config detected (new) |

### Up-to-date Skills
| Skill | Status |
|-------|--------|
| security | Current |
| frontend | Current |

### Skills Needing Updates
| Skill | Reason |
|-------|--------|
| database | Drizzle updated from 0.29 to 0.35 |
| testing | New test patterns detected |

### Missing Skills
| Technology | Recommended Skill |
|------------|-------------------|
| Playwright | testing (E2E section) |
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
- Remove orphaned skills manually
```

## Phase 5: Offer Actions

Based on the audit, offer to:
1. Refresh outdated skills
2. Generate missing skills
3. Remove orphaned skills

Ask user which actions to take before proceeding.
