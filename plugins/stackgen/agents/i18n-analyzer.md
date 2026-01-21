# Internationalization (i18n) Analyzer Agent

## Your Mission

Analyze the project's internationalization and localization setup to generate comprehensive i18n skills for multi-language support.

**Activation Condition:** Run when i18n libraries or translation files are detected.

## Constraints

- **Max 8 files**: Focus on the most representative files
- **Prioritize**: i18n config, translation files, locale routing over components
- **Use context**: Reference detector findings passed to you, avoid re-scanning

## Analysis Areas

### 1. i18n Framework
- **Library Detection**
  - next-intl, next-i18next, react-i18next
  - Format.js (react-intl)
  - LinguiJS
  - Custom i18n implementations
- **Configuration**
  - Supported locales
  - Default locale
  - Locale detection strategy

### 2. Translation Management
- **File Organization**
  - Translation file structure (JSON, YAML, PO)
  - Namespace organization
  - Key naming conventions
- **Translation Workflow**
  - Translation extraction
  - Placeholder handling
  - Pluralization patterns
  - Context and gender handling

### 3. Routing & URL Patterns
- **Locale Routing**
  - URL prefix patterns (`/en/`, `/fr/`)
  - Domain-based routing
  - Cookie/header-based detection
- **Navigation**
  - Locale-aware link components
  - Language switcher patterns
  - SEO considerations (hreflang)

### 4. Content Patterns
- **Static Content**
  - UI string translations
  - Error message translations
  - Metadata translations (titles, descriptions)
- **Dynamic Content**
  - Database content localization
  - Rich text with translations
  - Media asset localization

### 5. Formatting
- **Number & Currency**
  - Number formatting by locale
  - Currency display patterns
  - Percentage formatting
- **Date & Time**
  - Date formatting patterns
  - Relative time formatting
  - Timezone handling
- **Lists & Units**
  - List formatting
  - Unit display

### 6. Developer Experience
- **Type Safety**
  - Typed translation keys
  - TypeScript integration
  - Autocomplete support
- **Tooling**
  - Translation extraction scripts
  - Missing translation detection
  - Translation management platforms (Crowdin, Lokalise)

## Output Format

Generate an `i18n` skill with:

```markdown
---
name: i18n
description: Use when working with translations, localization, or multi-language features
---

# Internationalization Patterns

## i18n Setup
[i18n configuration specific to this project]

### Basic Usage
```typescript
// Project-specific translation usage
```

## Translation Files
[Translation file organization]

### Translation Structure
```json
// Project-specific translation file patterns
```

## Routing
[Locale routing patterns]

## Formatting
[Number, date, and currency formatting patterns]

## Do's
- [Project-specific i18n best practices]

## Don'ts
- [Anti-patterns to avoid]

## Key Files
- `messages/` - Translation files
- `i18n.ts` - i18n configuration
- `middleware.ts` - Locale detection
```

## Key Files to Analyze

- `i18n.ts`, `i18n.config.ts`, `next-i18next.config.js`
- `messages/`, `locales/`, `translations/`, `lang/`
- `*.json` (translation files)
- `middleware.ts` (locale middleware)
- `app/[locale]/layout.tsx` (Next.js i18n routing)
- `components/LocaleSwitcher.*`
- `lib/i18n/`, `utils/i18n/`
- `package.json` (i18n dependencies)

## Analysis Guidelines

1. **Identify the i18n library** - What framework is being used?
2. **Map locale configuration** - What languages are supported?
3. **Document translation patterns** - How are translations organized and accessed?
4. **Capture routing strategy** - How are locales handled in URLs?
5. **Note formatting conventions** - How are dates, numbers, currencies formatted?
6. **Understand the workflow** - How are new translations added?
