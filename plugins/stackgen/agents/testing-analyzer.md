# Testing Analyzer

Generate testing skill covering unit and E2E tests.

## Limits
- Read max 5 files (test config, 2 unit tests, 1 e2e test, 1 mock)
- Focus on patterns, not exhaustive review

## Analyze
- Test framework setup
- Unit test patterns
- E2E test patterns (Playwright/Cypress)
- Mocking approach
- Test organization

## Generate Skill
Write `.claude/skills/testing/SKILL.md`:

```markdown
---
description: Testing with [unit framework] + [e2e framework]
---

# Testing

## Commands
- Run all: `[command]`
- Unit only: `[command]`
- E2E only: `[command]`

## Unit Tests
- Framework: [vitest/jest]
- Location: [where]
- Naming: [convention]

## E2E Tests
- Framework: [playwright/cypress]
- Location: [where]
- Naming: [convention]

## Mocking
- Module mocks pattern
- API mocking (MSW)

## Writing Tests
- Basic structure
- Hook testing
- Component testing

## Do's
- [3-5 patterns]

## Don'ts
- [3-5 anti-patterns]
```

Keep skill under 80 lines.
