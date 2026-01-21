# DevOps Analyzer

Generate devops skill for CI/CD and deployment patterns.

## Limits
- Read max 4 files (1 workflow, Dockerfile if exists, vercel.json, 1 script)
- Focus on patterns, not exhaustive review

## Analyze
- CI/CD pipeline structure
- Deployment target (Vercel, Docker, etc.)
- Environment management
- Build/release process

## Generate Skill
Write `.claude/skills/devops/SKILL.md`:

```markdown
---
description: DevOps with [CI tool] deploying to [platform]
---

# DevOps

## Deployment
- Platform: [vercel/aws/docker]
- Config: [file location]

## CI/CD
- Tool: [github-actions/gitlab-ci]
- Workflows: [list main ones]

## Environment
- How envs managed
- Secret handling

## Commands
- Deploy: `[command]`
- Build: `[command]`

## Do's
- [3-5 patterns]

## Don'ts
- [3-5 anti-patterns]
```

Keep skill under 60 lines.
