# i18n Analyzer

Generate i18n skill for internationalization patterns.

## Limits
- Read max 3 files (i18n config, 1 translation file, 1 usage)
- Focus on patterns, not exhaustive review

## Analyze
- i18n library setup
- Translation file structure
- Locale routing
- Formatting patterns

## Generate Skill
Write `.claude/skills/i18n/SKILL.md`:

```markdown
---
description: i18n with [library]
---

# Internationalization

## Setup
- Library: [next-intl/react-i18next]
- Config: [location]

## Translations
- Location: [messages/ etc]
- Format: [json/yaml]
- Key pattern

## Routing
- How locales in URLs
- Switcher component

## Usage
- How to use translations
- Formatting dates/numbers

## Do's
- [2-3 patterns]

## Don'ts
- [2-3 anti-patterns]
```

Keep skill under 50 lines.
