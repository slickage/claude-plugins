# claude-plugins

Custom Claude Code plugins for codebase analysis and skill generation.

## Installation

### Add the Marketplace

```bash
/plugin marketplace add crod951/claude-plugins
```

### Install a Plugin

```bash
/plugin install stackgen@crod951
```

## Available Plugins

### stackgen (v1.0.0)

Analyzes codebases and generates tailored Claude Code skills. **11 specialized agents** with optimized context passing.

#### Features

- **Fast Detection** - Single stack-detector (Haiku) analyzes dependencies, configs, structure
- **Context Passing** - Detector findings passed to analyzers to avoid redundant reads
- **Tech Gating** - Only spawns analyzers for detected technologies
- **8-File Limits** - Each analyzer reads max 8 files for efficiency

#### Commands

| Command | Description |
|---------|-------------|
| `/stackgen:analyze` | Full codebase analysis and skill generation |
| `/stackgen:quick` | Quick tech stack overview |
| `/stackgen:refresh` | Update existing skills |
| `/stackgen:check` | Audit skills for issues |

#### Agents

**Detection (1):**
- `stack-detector` - Comprehensive dependency/config/pattern analysis (Haiku)

**Core Analyzers (4, always run):**
- `security-analyzer` - Security patterns and best practices
- `architecture-analyzer` - Code structure and organization
- `code-quality-analyzer` - Linting, formatting, types, dependencies
- `performance-analyzer` - Performance optimization

**Conditional Analyzers (6, gated by detection):**
- `frontend-analyzer` - UI framework patterns (React, Vue, Angular)
- `backend-analyzer` - Server patterns (APIs, Server Actions)
- `database-analyzer` - ORM/database patterns
- `testing-analyzer` - Unit, integration, E2E tests
- `devops-analyzer` - CI/CD, Docker, deployment
- `monitoring-analyzer` - Logging, error tracking, analytics

#### Generated Skills

```
.claude/skills/
├── security/
├── architecture/
├── code-quality/
├── performance/
├── frontend/       (if detected)
├── backend/        (if detected)
├── database/       (if detected)
├── testing/        (if detected)
├── devops/         (if detected)
└── monitoring/     (if detected)
```

## Workflow

1. **Analyze project**: `/stackgen:analyze`
2. **Quick context**: `/stackgen:quick`
3. **After upgrades**: `/stackgen:refresh`
4. **Maintenance**: `/stackgen:check`

## License

MIT
