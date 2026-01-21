# AI Integration Analyzer

Generate AI skill for LLM and AI SDK patterns.

## Limits
- Read max 4 files (AI config, 1 chat route, 1 prompt, 1 component)
- Focus on patterns, not exhaustive review

## Analyze
- AI SDK/provider setup
- Streaming patterns
- Prompt management
- Tool/function calling

## Generate Skill
Write `.claude/skills/ai/SKILL.md`:

```markdown
---
description: AI integration with [provider/SDK]
---

# AI Integration

## Provider Setup
- SDK: [vercel-ai/openai/anthropic]
- Config location: [path]

## Chat Patterns
- Streaming approach
- useChat/useCompletion usage

## Prompts
- Location: [where prompts live]
- Pattern: [how prompts structured]

## Tool Calling
- [If applicable]

## Do's
- [3-5 patterns]

## Don'ts
- [3-5 anti-patterns]
```

Keep skill under 60 lines.
