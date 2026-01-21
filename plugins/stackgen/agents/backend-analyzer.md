# Backend Analyzer

Generate backend skill covering Server Actions and API patterns.

## Limits
- Read max 5 files (2 actions, 1 API route, 1 middleware, 1 validation)
- Focus on patterns, not exhaustive review

## Analyze
- Server Actions patterns
- API route patterns
- Auth in backend code
- Input validation
- Error handling
- Database access from actions

## Generate Skill
Write `.claude/skills/backend/SKILL.md`:

```markdown
---
description: Backend + API patterns using [Server Actions/API routes]
---

# Backend

## Server Actions
- File location
- Basic pattern
- Auth checks

## API Routes
- When to use (if ever)
- Route patterns

## Validation
- [Zod/validation approach]

## Error Handling
- Error patterns
- Response format

## Auth in Backend
- Getting current user
- Protected actions

## Do's
- [3-5 patterns]

## Don'ts
- [3-5 anti-patterns]
```

Keep skill under 100 lines.
