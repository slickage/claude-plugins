# stackgen

Analyzes codebases and generates tailored Claude Code skills for optimal AI-assisted development.

## Features

- **Parallel Detection** - 4 specialized detectors run simultaneously for fast analysis
- **Pattern Analysis** - Understands your code conventions and architecture
- **Skill Generation** - Creates project-specific guidance for Claude
- **22 Specialized Agents** - Deep analysis across all aspects of modern development

## Commands

| Command | Description |
|---------|-------------|
| `/stackgen:analyze` | Full codebase analysis and skill generation |
| `/stackgen:quick` | Quick tech stack overview |
| `/stackgen:refresh` | Update existing skills |
| `/stackgen:check` | Audit skills for issues |

## Usage

### Initial Analysis

```bash
/stackgen:analyze
```

Spawns parallel detectors, then runs analyzer agents to generate skills in `.claude/skills/`.

### Quick Reference

```bash
/stackgen:quick
```

Instant tech stack overview without generating files.

### Refresh Skills

```bash
/stackgen:refresh           # Refresh all
/stackgen:refresh react     # Refresh specific
```

### Check Skills

```bash
/stackgen:check
```

Audit for outdated patterns or missing skills.

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                 DETECTION (Parallel)                    │
├──────────────┬──────────────┬─────────────┬─────────────┤
│ dependency-  │ config-      │ structure-  │ pattern-    │
│ detector     │ detector     │ detector    │ detector    │
└──────────────┴──────────────┴─────────────┴─────────────┘
                          │
                    Merge Results
                          │
┌─────────────────────────────────────────────────────────┐
│                 ANALYSIS (Parallel)                     │
├─────────┬─────────┬─────────┬─────────┬─────────┬───────┤
│security │perform- │architec-│ react   │database │ ...   │
│analyzer │ance     │ture     │analyzer │analyzer │       │
└─────────┴─────────┴─────────┴─────────┴─────────┴───────┘
                          │
                   Generate Skills
```

## Generated Skills

### Core (Always Generated)

| Skill | Analyzer |
|-------|----------|
| `security` | security-analyzer |
| `performance` | performance-analyzer |
| `architecture` | architecture-analyzer |
| `dependency-management` | dependency-analyzer |
| `code-quality` | code-quality-analyzer |

### Conditional (Based on Detection)

| Skill | Trigger | Analyzer |
|-------|---------|----------|
| `react` | React detected | react-analyzer |
| `database` | ORM detected | database-analyzer |
| `testing` | Test framework | testing-analyzer |
| `e2e-testing` | Playwright/Cypress | e2e-testing-analyzer |
| `frontend` | Frontend framework | frontend-analyzer |
| `backend` | Backend patterns | backend-analyzer |
| `api` | API routes | api-analyzer |
| `devops` | Docker/CI-CD | devops-analyzer |
| `monitoring` | Sentry/logging | monitoring-analyzer |
| `i18n` | i18n library | i18n-analyzer |
| `monorepo` | Turborepo/Nx | monorepo-analyzer |
| `ai` | AI SDK | ai-integration-analyzer |

## Agents (22 Total)

### Detection Agents (4)
Run in parallel for fast tech stack detection:
- `dependency-detector` - Scans package.json, requirements.txt, Cargo.toml
- `config-detector` - Scans tsconfig, eslint, framework configs
- `structure-detector` - Analyzes directory structure and organization
- `pattern-detector` - Samples source files for coding patterns

### Core Analyzers (6)
Always run to generate foundational skills:
- `tech-stack-detector` - Legacy unified detector
- `security-analyzer` - Security patterns
- `performance-analyzer` - Performance optimization
- `architecture-analyzer` - Code structure
- `dependency-analyzer` - Package management
- `code-quality-analyzer` - Linting, formatting

### Conditional Analyzers (12)
Run based on detected technologies:
- `react-analyzer` - React patterns
- `database-analyzer` - ORM/database patterns
- `testing-analyzer` - Unit/integration tests
- `e2e-testing-analyzer` - E2E test patterns
- `frontend-analyzer` - Frontend framework patterns
- `backend-analyzer` - Backend patterns
- `api-analyzer` - API design patterns
- `ai-integration-analyzer` - LLM/AI patterns
- `devops-analyzer` - CI/CD, Docker
- `monitoring-analyzer` - Logging, error tracking
- `i18n-analyzer` - Internationalization
- `monorepo-analyzer` - Workspace patterns

## Customization

Add custom content that survives refresh:

```markdown
<!-- CUSTOM: Keep this section -->
[Your custom additions]
<!-- /CUSTOM -->
```

## Workflow

1. **On Project Join**: `/stackgen:analyze`
2. **Quick Context**: `/stackgen:quick`
3. **After Upgrades**: `/stackgen:refresh`
4. **Periodic Audit**: `/stackgen:check`

## License

MIT
