# Architecture Analyzer

Generate architecture skill based on detected code organization.

## Limits
- Read max 3 files (look at structure via Glob/LS, not content)
- Focus on conventions, not exhaustive mapping

## Analyze
- Directory organization pattern
- Module/feature boundaries
- Import conventions
- Naming conventions
- File colocation patterns

## Generate Skill
Write `.claude/skills/architecture/SKILL.md`:

```markdown
---
description: Code organization for [architecture-style]
---

# Architecture

## Structure
- [Directory layout explanation]

## Conventions
- [File naming: kebab-case, etc.]
- [Export style: named vs default]
- [Import ordering]

## Adding New Features
- [Where to put new code]
- [How to structure new modules]

## Do's
- [3-5 specific patterns]

## Don'ts
- [3-5 anti-patterns]
```

Keep skill under 80 lines.
