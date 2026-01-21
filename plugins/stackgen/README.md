# stackgen

Analyzes codebases and generates tailored Claude Code skills.

## Features

- **Fast Detection** - 4 parallel detectors using Haiku model
- **Context Passing** - Analyzers skip re-scanning with pre-detected context
- **18 Specialized Agents** - Optimized for minimal token usage

## Commands

| Command | Description |
|---------|-------------|
| `/stackgen:analyze` | Full analysis and skill generation |
| `/stackgen:quick` | Quick tech stack overview |
| `/stackgen:refresh` | Update existing skills |
| `/stackgen:check` | Audit skills for issues |

## Usage

```bash
/stackgen:analyze     # Full analysis
/stackgen:quick       # Quick overview
/stackgen:refresh     # Update skills
/stackgen:check       # Audit skills
```

## Architecture

```
Detection (Haiku, parallel)
    │
    ├─ dependency-detector
    ├─ config-detector
    ├─ structure-detector
    └─ pattern-detector
    │
    ▼
Merge Context
    │
    ▼
Analysis (parallel, with context)
    │
    ├─ 5 core analyzers (always)
    └─ up to 9 conditional analyzers
    │
    ▼
Skills in .claude/skills/
```

## Generated Skills

### Core (Always)
| Skill | Analyzer |
|-------|----------|
| security | security-analyzer |
| performance | performance-analyzer |
| architecture | architecture-analyzer |
| dependency-management | dependency-analyzer |
| code-quality | code-quality-analyzer |

### Conditional
| Skill | Trigger |
|-------|---------|
| frontend | React/Vue/Svelte detected |
| backend | Server Actions/API routes |
| database | ORM detected |
| testing | Test framework detected |
| devops | Docker/CI-CD detected |
| monitoring | Sentry/logging detected |
| i18n | i18n library detected |
| monorepo | Turborepo/Nx detected |
| ai | AI SDK detected |

## Agents (18 Total)

### Detectors (4) - Use Haiku
- `dependency-detector` - package.json, requirements.txt
- `config-detector` - tsconfig, eslint, framework configs
- `structure-detector` - Directory organization
- `pattern-detector` - Source file patterns

### Core Analyzers (5)
- `security-analyzer`
- `performance-analyzer`
- `architecture-analyzer`
- `dependency-analyzer`
- `code-quality-analyzer`

### Conditional Analyzers (9)
- `frontend-analyzer` - UI + React patterns
- `backend-analyzer` - Server Actions + API
- `database-analyzer` - ORM patterns
- `testing-analyzer` - Unit + E2E tests
- `devops-analyzer` - CI/CD + Docker
- `monitoring-analyzer` - Error tracking + logging
- `i18n-analyzer` - Internationalization
- `monorepo-analyzer` - Workspace patterns
- `ai-integration-analyzer` - LLM integration

## Token Optimization

- Detectors use Haiku (~70% cheaper)
- File read limits (3-5 files per agent)
- Context passing eliminates re-scanning
- Merged agents reduce redundant analysis
- Concise skill output (~50-100 lines)

## Customization

Add persistent content:
```markdown
<!-- CUSTOM: Keep this -->
Your additions here
<!-- /CUSTOM -->
```

## License

MIT
