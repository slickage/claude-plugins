---
description: Audit skills for outdated or missing patterns
---

# Check Skills

Audit existing skills against current codebase.

## Phase 1: Inventory

List all skills in `.claude/skills/`.

If none exist:
```
No skills found. Run `/stackgen:analyze` first.
```

## Phase 2: Detect Current Stack (Haiku)

**Spawn all 4 detectors using `model: haiku`:**

```
Task calls (all in ONE message):
- subagent_type: stackgen:dependency-detector, model: haiku
- subagent_type: stackgen:config-detector, model: haiku
- subagent_type: stackgen:structure-detector, model: haiku
- subagent_type: stackgen:pattern-detector, model: haiku
```

## Phase 3: Compare

Match existing skills against detected tech:

- **Up-to-date** - Skill matches current stack
- **Outdated** - Version changes or new patterns
- **Missing** - Tech detected but no skill
- **Orphaned** - Skill for removed tech

## Phase 4: Report

```
## Skill Audit

**Summary:** 8 skills, 2 need updates, 1 missing

### Needs Update
| Skill | Reason |
|-------|--------|
| database | Drizzle 0.29→0.35 |
| testing | Playwright added |

### Missing
| Tech | Suggested |
|------|-----------|
| Sentry | monitoring |

### Orphaned
| Skill | Reason |
|-------|--------|
| (none) | |

**Recommended:**
- `/stackgen:refresh database,testing`
- `/stackgen:analyze` for missing
```

## Phase 5: Offer Actions

Ask user which to execute:
1. Refresh outdated
2. Generate missing
3. Remove orphaned
