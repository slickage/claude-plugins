# Code Quality Analyzer

Generate code quality skill based on linting/formatting setup.

## Limits
- Read max 3 config files (eslint, prettier/biome, tsconfig)
- Focus on key rules, not exhaustive list

## Analyze
- ESLint key rules
- Prettier/Biome formatting
- TypeScript strictness
- Import organization

## Generate Skill
Write `.claude/skills/code-quality/SKILL.md`:

```markdown
---
description: Code quality with [linter] + [formatter]
---

# Code Quality

## TypeScript
- Strict mode: [yes/no]
- Key settings: [notable ones]

## Linting
- Tool: [eslint/biome]
- Key rules: [2-3 important ones]

## Formatting
- Tool: [prettier/biome]
- Style: [key settings]

## Commands
- Lint: `[command]`
- Format: `[command]`
- Type check: `[command]`

## Do's
- [2-3 patterns]

## Don'ts
- [2-3 anti-patterns]
```

Keep skill under 60 lines.
