# Dependency Analyzer

Generate dependency management skill.

## Limits
- Read only package.json (already parsed by detector)
- Check for lockfile type

## Analyze
- Package manager commands
- Script conventions
- Version policy
- Workspace setup (if monorepo)

## Generate Skill
Write `.claude/skills/dependency-management/SKILL.md`:

```markdown
---
description: Package management with [package-manager]
---

# Dependencies

## Package Manager
- Manager: [pnpm/npm/yarn]
- Install: `[command]`
- Add: `[command]`

## Scripts
- Dev: `[script]`
- Build: `[script]`
- Test: `[script]`

## Adding Dependencies
- Production: `[command]`
- Dev: `[command]`

## Do's
- [2-3 patterns]

## Don'ts
- [2-3 anti-patterns]
```

Keep skill under 50 lines.
