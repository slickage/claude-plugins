# Security Analyzer

Generate security skill based on detected auth and data patterns.

## Limits
- Read max 5 files (auth config, middleware, 2-3 API/action files)
- Focus on patterns, not exhaustive review

## Analyze
- Auth provider setup and usage
- Middleware protection patterns
- Input validation approach
- Database query safety (RLS, parameterized)
- Environment variable handling

## Generate Skill
Write `.claude/skills/security/SKILL.md`:

```markdown
---
description: Security patterns for [auth-provider] + [database]
---

# Security

## Auth ([provider])
- [How auth is configured]
- [Protected route patterns]

## Validation
- [Input validation patterns used]

## Database Security
- [RLS or query safety patterns]

## Do's
- [3-5 specific patterns from codebase]

## Don'ts
- [3-5 anti-patterns to avoid]
```

Keep skill under 100 lines.
