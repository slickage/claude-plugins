# Database Analyzer

Generate database skill for ORM and query patterns.

## Limits
- Read max 4 files (schema, 1 migration, 2 query files)
- Focus on patterns, not exhaustive review

## Analyze
- ORM and database type
- Schema patterns
- Query patterns
- Migration workflow
- RLS if present

## Generate Skill
Write `.claude/skills/database/SKILL.md`:

```markdown
---
description: Database patterns with [ORM] + [database]
---

# Database

## Stack
- ORM: [drizzle/prisma]
- Database: [postgresql/etc]
- Host: [supabase/etc]

## Schema
- Location: [path]
- Pattern: [how schemas defined]

## Queries
- Basic CRUD pattern
- Relations/joins

## Migrations
- Generate: `[command]`
- Apply: `[command]`

## Security
- RLS patterns (if any)
- Access control

## Do's
- [3-5 patterns]

## Don'ts
- [3-5 anti-patterns]
```

Keep skill under 80 lines.
