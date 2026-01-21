# Frontend Analyzer

Generate frontend skill covering UI framework and React patterns.

## Limits
- Read max 5 files (1 layout, 2 components, 1 form, 1 ui component)
- Focus on patterns, not exhaustive review

## Analyze
- Component patterns (server vs client)
- Styling approach (tailwind, css modules)
- Form handling
- UI library usage (shadcn, radix)
- State management
- React patterns (hooks, composition)

## Generate Skill
Write `.claude/skills/frontend/SKILL.md`:

```markdown
---
description: Frontend + React patterns for [framework]
---

# Frontend

## Components
- Server vs client patterns
- File structure conventions

## Styling
- [Tailwind/CSS approach]
- Responsive patterns

## UI Library
- [shadcn/radix usage]
- Component imports

## Forms
- [Form library + validation]

## State
- [State management approach]

## React Patterns
- Hooks conventions
- Composition patterns

## Do's
- [3-5 patterns]

## Don'ts
- [3-5 anti-patterns]
```

Keep skill under 100 lines.
