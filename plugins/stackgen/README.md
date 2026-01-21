# stackgen

Analyzes codebases and generates tailored Claude Code skills for optimal AI-assisted development.

## Features

- **Fast Detection** - Single stack-detector agent analyzes dependencies, configs, structure, and patterns
- **Context Passing** - Detector findings are passed to analyzers to avoid redundant file reads
- **Tech Detection Gating** - Only spawns analyzers for detected technologies
- **8-File Limits** - Each analyzer reads max 8 files for efficient analysis
- **11 Specialized Agents** - Focused analysis across essential development areas

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

Runs stack-detector (Haiku), then spawns relevant analyzers in parallel to generate skills in `.claude/skills/`.

### Quick Reference

```bash
/stackgen:quick
```

Instant tech stack overview without generating files.

### Refresh Skills

```bash
/stackgen:refresh           # Refresh all
/stackgen:refresh frontend  # Refresh specific
```

### Check Skills

```bash
/stackgen:check
```

Audit for outdated patterns or missing skills.

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    DETECTION                            │
│              stack-detector (Haiku)                     │
│   Dependencies + Configs + Structure + Patterns         │
└────────────────────────┬────────────────────────────────┘
                         │
              Detection JSON + Context
                         │
┌─────────────────────────────────────────────────────────┐
│            ANALYSIS (Parallel, Gated)                   │
├─────────┬─────────┬─────────┬─────────┬─────────┬───────┤
│security │frontend │backend  │database │testing  │ ...   │
│(always) │(if React)│(if API)│(if DB) │(if test)│       │
└─────────┴─────────┴─────────┴─────────┴─────────┴───────┘
     │         │         │         │         │
     │    Each analyzer receives context from detector
     │
                   Generate Skills
```

## Generated Skills

### Core (Always Generated)

| Skill | Analyzer |
|-------|----------|
| `security` | security-analyzer |
| `architecture` | architecture-analyzer |
| `code-quality` | code-quality-analyzer (includes dependency management) |
| `performance` | performance-analyzer |

### Conditional (Based on Detection)

| Skill | Trigger | Analyzer |
|-------|---------|----------|
| `frontend` | React/Vue/Angular | frontend-analyzer |
| `backend` | Server Actions/Express | backend-analyzer |
| `database` | PostgreSQL/MongoDB/ORM | database-analyzer |
| `testing` | Vitest/Jest/Playwright | testing-analyzer |
| `devops` | Docker/CI-CD | devops-analyzer |
| `monitoring` | Sentry/logging | monitoring-analyzer |

## Agents (11 Total)

### Detection Agent (1)

Runs with Haiku model for fast detection:
- `stack-detector` - Comprehensive analysis of dependencies, configs, structure, and patterns

### Analyzers (10)

**Core Analyzers (Always Run):**
- `security-analyzer` - Security patterns and best practices
- `architecture-analyzer` - Code structure and organization
- `code-quality-analyzer` - Linting, formatting, types, dependency management
- `performance-analyzer` - Performance optimization

**Conditional Analyzers (Gated by Detection):**
- `frontend-analyzer` - UI framework patterns (React, Vue, Angular)
- `backend-analyzer` - Server patterns (APIs, Server Actions)
- `database-analyzer` - ORM/database patterns
- `testing-analyzer` - Unit, integration, and E2E tests
- `devops-analyzer` - CI/CD, Docker, deployment
- `monitoring-analyzer` - Logging, error tracking, analytics

## Optimizations

### Context Passing
The stack-detector passes pre-analyzed findings to each analyzer:
```
<detector-context>
Frontend: React 18 with Next.js App Router
Key files: src/app/layout.tsx, src/components/...
Patterns: Server Components, Tailwind CSS
</detector-context>
```

This prevents analyzers from re-reading the same files.

### 8-File Limits
Each analyzer reads a maximum of 8 files, focusing on:
- Entry points and configs
- Core modules and patterns
- Representative examples

### Tech Detection Gating
Analyzers only spawn if their relevant technology is detected:
- No `database-analyzer` without a database/ORM
- No `frontend-analyzer` without a UI framework
- No `testing-analyzer` without test frameworks

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
