# Monitoring Analyzer

Generate monitoring skill for error tracking and logging.

## Limits
- Read max 3 files (sentry config, logger, 1 usage example)
- Focus on patterns, not exhaustive review

## Analyze
- Error tracking setup (Sentry, etc.)
- Logging patterns
- Analytics if present

## Generate Skill
Write `.claude/skills/monitoring/SKILL.md`:

```markdown
---
description: Monitoring with [error tracker] + [logger]
---

# Monitoring

## Error Tracking
- Tool: [sentry/etc]
- Config: [location]
- Usage pattern

## Logging
- Library: [pino/winston/console]
- Log levels used
- Structured logging pattern

## Analytics
- [If applicable]

## Do's
- [3-5 patterns]

## Don'ts
- [3-5 anti-patterns]
```

Keep skill under 50 lines.
