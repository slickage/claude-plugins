# Performance Analyzer

Generate performance skill based on detected optimization patterns.

## Limits
- Read max 5 files (config, 2 components, 1 data file, 1 API)
- Focus on patterns, not exhaustive review

## Analyze
- Caching strategies (tanstack-query, unstable_cache)
- Image optimization (next/image usage)
- Code splitting patterns
- Server vs client component balance
- Database query efficiency

## Generate Skill
Write `.claude/skills/performance/SKILL.md`:

```markdown
---
description: Performance patterns for [framework]
---

# Performance

## Data Caching
- [Caching patterns used]

## Component Optimization
- [Server/client patterns]
- [Lazy loading patterns]

## Images
- [Image optimization approach]

## Do's
- [3-5 specific patterns]

## Don'ts
- [3-5 anti-patterns]
```

Keep skill under 80 lines.
