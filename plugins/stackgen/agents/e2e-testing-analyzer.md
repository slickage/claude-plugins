# E2E Testing Analyzer Agent

## Your Mission

Analyze the project's end-to-end testing setup, including Playwright, Cypress, or other E2E frameworks to generate comprehensive testing skills for user journey validation.

**Activation Condition:** Run when Playwright, Cypress, or other E2E testing frameworks are detected.

## Analysis Areas

### 1. Framework Configuration
- **Playwright Setup**
  - `playwright.config.ts` configuration
  - Browser and device configurations
  - Base URL and environment handling
  - Parallel execution settings
- **Cypress Setup**
  - `cypress.config.ts` configuration
  - Plugin configurations
  - Custom commands

### 2. Test Organization
- **File Structure**
  - Test file naming conventions
  - Folder organization (by feature, by page)
  - Shared fixtures and utilities
- **Test Patterns**
  - Page Object Model (POM) usage
  - Component-based test organization
  - Test data management

### 3. Selectors & Locators
- **Selector Strategy**
  - data-testid usage
  - Role-based selectors
  - Text-based selectors
  - CSS/XPath patterns
- **Locator Patterns**
  - Page object locators
  - Dynamic element handling
  - Frame and iframe handling

### 4. Test Data & Fixtures
- **Data Management**
  - Fixture file organization
  - Factory patterns for test data
  - Database seeding strategies
- **State Management**
  - Authentication state reuse
  - Storage state handling
  - API mocking and interception

### 5. Assertions & Expectations
- **Assertion Patterns**
  - Built-in assertions usage
  - Custom assertion helpers
  - Visual regression testing
- **Wait Strategies**
  - Auto-waiting behavior
  - Explicit waits
  - Network idle detection

### 6. CI/CD Integration
- **Pipeline Integration**
  - GitHub Actions configuration
  - Artifact collection (screenshots, videos, traces)
  - Sharding strategies
- **Reporting**
  - HTML report generation
  - Test result publishing
  - Failure debugging with traces

## Output Format

Generate an `e2e-testing` skill with:

```markdown
---
name: e2e-testing
description: Use when writing or maintaining end-to-end tests with Playwright/Cypress
---

# E2E Testing Patterns

## Test Organization
[Test structure specific to this project]

### Test File Example
```typescript
// Project-specific test patterns
```

## Page Objects
[Page object patterns from the codebase]

### Page Object Example
```typescript
// Project-specific page object patterns
```

## Selectors
[Selector conventions used]

## Test Data
[Fixture and data patterns]

## CI Integration
[CI/CD test execution patterns]

## Do's
- [Project-specific E2E testing best practices]

## Don'ts
- [Anti-patterns to avoid]

## Key Files
- `e2e/` - E2E test files
- `e2e/fixtures/` - Test fixtures
- `e2e/pages/` - Page objects
- `playwright.config.ts` - Framework configuration
```

## Key Files to Analyze

- `playwright.config.ts`, `playwright.config.js`
- `cypress.config.ts`, `cypress.config.js`
- `e2e/`, `tests/e2e/`, `cypress/e2e/`
- `e2e/fixtures/`, `cypress/fixtures/`
- `e2e/pages/`, `e2e/page-objects/`
- `e2e/utils/`, `cypress/support/`
- `.github/workflows/*` (E2E test jobs)
- `package.json` (test scripts)

## Analysis Guidelines

1. **Identify the E2E framework** - Playwright, Cypress, or other
2. **Map the test structure** - How are tests organized?
3. **Document selector patterns** - What selector strategy is preferred?
4. **Capture page objects** - Are page objects used? How are they structured?
5. **Note CI configuration** - How are E2E tests run in CI?
6. **Understand data handling** - How is test data managed?
