---
name: check
description: Validate generated skills against current codebase and identify outdated or missing patterns
---

# Check Skills

## Purpose

Audit the existing generated skills to identify:
- Skills that are outdated and need refreshing
- Missing skills for detected technologies
- Patterns in skills that no longer match the codebase
- Opportunities for new skill generation

## Invocation

```
/stackgen:check
```

## Process

### Step 1: Inventory Existing Skills

Scan `.claude/skills/` and list all generated skills with their metadata:

- Skill name
- Last generated/modified date (from file metadata)
- Description from frontmatter

### Step 2: Run Tech Stack Detection

Execute the `tech-stack-detector` agent to get current technology inventory.

### Step 3: Compare Skills vs. Technologies

Create a comparison matrix:

```
## Skills Audit Report

### Up-to-Date
| Skill | Technology | Version Match |
|-------|------------|---------------|
| react | React 19.0 | Current |
| database | Prisma 5.x | Current |

### Need Update
| Skill | Issue |
|-------|-------|
| testing | Vitest upgraded from 1.x to 2.x |
| frontend | New shadcn/ui components added |

### Missing Skills
| Technology | Recommended Skill |
|------------|-------------------|
| Playwright | e2e-testing |
| Sentry | monitoring |
| pnpm workspaces | monorepo |

### Orphaned Skills
| Skill | Issue |
|-------|-------|
| graphql | No GraphQL detected in project |
```

### Step 4: Pattern Validation

For each existing skill, sample-check that referenced patterns still exist:

- Check if referenced files still exist
- Verify code examples are still accurate
- Ensure version-specific guidance matches current versions

### Step 5: Generate Recommendations

```
## Recommendations

### High Priority
1. **Regenerate `testing` skill** - Major version change detected
2. **Generate `monitoring` skill** - Sentry integration found but no skill exists

### Medium Priority
3. **Update `react` skill** - New patterns detected in components/
4. **Consider `ai` skill** - AI SDK dependency added

### Low Priority
5. **Review `architecture` skill** - File structure unchanged, patterns still valid

### Action
Run `/stackgen:refresh --all` to update all skills
Or run `/stackgen:refresh [skill-name]` for specific updates
```

## Validation Checks

For each skill, verify:

1. **File References** - Do referenced files in "Key Files" section exist?
2. **Version Accuracy** - Do version numbers match package.json?
3. **Pattern Validity** - Do code examples reflect current codebase patterns?
4. **Completeness** - Are there new patterns that should be documented?

## Output Format

The check should produce:
- **Summary table** of all skills and their status
- **Detailed findings** for any issues
- **Actionable recommendations** prioritized by impact
- **Command suggestions** for fixing issues

## Integration

This skill works well as part of a workflow:

1. Run `/stackgen:check` to audit
2. Review recommendations
3. Run `/stackgen:refresh` for specific skills
4. Or run `/stackgen:analyze` to regenerate everything
