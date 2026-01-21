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

### stackgen (v2.0.0)

Analyzes codebases and generates tailored Claude Code skills. **18 specialized analyzers** for modern full-stack development.

#### Commands

| Command | Description |
|---------|-------------|
| `/stackgen:analyze` | Full codebase analysis and skill generation |
| `/stackgen:quick` | Quick tech stack overview |
| `/stackgen:refresh` | Update skills when codebase changes |
| `/stackgen:check` | Audit skills for issues |

#### What It Analyzes

**Core (Always Run):**
- Security patterns
- Performance optimization
- Architecture and structure
- Code quality
- Dependencies

**Conditional (Based on Detection):**
- React/Next.js patterns
- Database/ORM (Prisma, Drizzle)
- Testing (Vitest, Jest)
- E2E (Playwright, Cypress)
- API (REST, GraphQL, tRPC)
- DevOps (Docker, CI/CD)
- Monitoring (Sentry, logging)
- i18n (internationalization)
- Monorepo (Turborepo, Nx)
- AI (LLM APIs, AI SDK)

#### Generated Skills

```
.claude/skills/
├── security/
├── performance/
├── architecture/
├── code-quality/
├── react/          (if detected)
├── database/       (if detected)
├── testing/        (if detected)
├── devops/         (if detected)
├── monitoring/     (if detected)
└── ...
```

## Workflow

1. **Join project**: `/stackgen:analyze`
2. **Quick context**: `/stackgen:quick`
3. **After upgrades**: `/stackgen:refresh`
4. **Maintenance**: `/stackgen:check`

## Contributing

1. Fork this repository
2. Add your plugin to `plugins/`
3. Update `marketplace.json`
4. Submit a PR

## License

MIT
