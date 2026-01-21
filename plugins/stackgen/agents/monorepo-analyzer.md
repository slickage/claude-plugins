# Monorepo Analyzer

Generate monorepo skill for workspace patterns.

## Limits
- Read max 3 files (turbo.json/nx.json, workspace config, 1 package.json)
- Focus on patterns, not exhaustive review

## Analyze
- Monorepo tool (turborepo, nx, etc.)
- Package structure
- Build pipeline
- Shared configs

## Generate Skill
Write `.claude/skills/monorepo/SKILL.md`:

```markdown
---
description: Monorepo with [tool]
---

# Monorepo

## Structure
- Tool: [turborepo/nx]
- Layout: apps/ + packages/

## Packages
- apps/: [list]
- packages/: [list]

## Commands
- Build all: `[command]`
- Dev: `[command]`
- Add package: `[command]`

## Dependencies
- Internal dep pattern
- Shared config usage

## Do's
- [2-3 patterns]

## Don'ts
- [2-3 anti-patterns]
```

Keep skill under 50 lines.
