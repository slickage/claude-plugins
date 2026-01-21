---
description: Quick tech stack overview without generating files
---

# Quick Tech Stack Overview

Get an instant overview of the project's technology stack without generating skill files.

## Process

**Spawn all 4 detector agents in parallel** using multiple Task tool calls in a SINGLE message:

| Agent | Purpose |
|-------|---------|
| `stackgen:dependency-detector` | Scan package.json, requirements.txt, etc. |
| `stackgen:config-detector` | Scan tsconfig, eslint, framework configs |
| `stackgen:structure-detector` | Analyze directory structure |
| `stackgen:pattern-detector` | Sample source files for patterns |

**Task prompts:**
```
dependency-detector: Quick scan of dependency manifests. Return concise JSON summary.
config-detector: Quick scan of config files. Return concise JSON summary.
structure-detector: Quick directory structure analysis. Return concise JSON summary.
pattern-detector: Quick source file sampling. Return concise JSON summary.
```

## Merge and Display

After all 4 detectors complete, merge results and display:

```
## Tech Stack

### Framework
- **Runtime:** [from dependency-detector]
- **Framework:** [from config-detector]
- **Language:** [from config-detector]

### Frontend
- **UI Library:** [from dependency-detector]
- **Styling:** [from pattern-detector]
- **Components:** [from structure-detector]

### Backend
- **API Style:** [from pattern-detector]
- **Database:** [from config-detector]
- **Auth:** [from pattern-detector]

### Architecture
- **Style:** [from structure-detector]
- **Organization:** [from structure-detector]

### Infrastructure
- **Package Manager:** [from dependency-detector]
- **Deployment:** [from config-detector]
- **CI/CD:** [from config-detector]

### Testing
- **Unit:** [from config-detector]
- **E2E:** [from config-detector]

### Key Commands
- `dev` - [from dependency-detector scripts]
- `build` - [from dependency-detector scripts]
- `test` - [from dependency-detector scripts]
```

## After Output

Check if `.claude/skills/` exists:
- If skills exist: List them and note they're available
- If no skills: Suggest running `/stackgen:analyze` for full skill generation
