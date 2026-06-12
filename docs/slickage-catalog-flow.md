# slickage-catalog — which command, and what happens

Two commands. **`publish`** adds something to the team catalog. **`sync`** installs
from it. The diagram below renders on GitHub and in Notion (paste into a `mermaid`
code block).

```mermaid
flowchart TD
    Start([What do you want to do?])
    Start --> Q{Add a tool to the<br/>catalog, or install<br/>tools from it?}

    Q -->|"Share / add a tool"| PUB["<b>/slickage-catalog:publish</b>"]
    Q -->|"Set up my machine"| SYNC["<b>/slickage-catalog:sync</b>"]

    %% ---------- PUBLISH ----------
    subgraph PUBLISH["PUBLISH — add an entry to the catalog"]
        direction TB
        PUB --> P1{Is the tool yours,<br/>or third-party?}
        P1 -->|"Mine — local,<br/>not published yet"| HOST["HOST path"]
        P1 -->|"Third-party — already<br/>published elsewhere"| ENDORSE["ENDORSE path"]

        HOST --> H1["Scaffold a plugin into the repo<br/>(skill, hook, or mcp)"]
        H1 --> H2["Open a PR to<br/>slickage/claude-plugins"]
        H2 --> H3["Add a Notion catalog row<br/>Install = name@slickage"]

        ENDORSE --> E1["Add a Notion catalog row<br/>Install = upstream string<br/>(no hosting, no PR)"]
    end

    H3 --> MERGE([Teammate reviews + merges PR])
    MERGE --> LIVE([In the catalog, installable])
    E1 --> LIVE

    %% ---------- SYNC ----------
    subgraph SYNC_FLOW["SYNC — install endorsed tools onto your machine"]
        direction TB
        SYNC --> S1["Read the Notion catalog<br/>(Skills, MCP, Hooks)"]
        S1 --> S2["Diff against what you<br/>already have installed"]
        S2 --> S3{Anything missing?}
        S3 -->|"No"| INSYNC([You're in sync ✓])
        S3 -->|"Yes"| S4["Checklist — pick what<br/>you want to install"]
        S4 --> S5["claude plugin install<br/>the ones you picked"]
        S5 --> S6["Manual tools (brew CLIs,<br/>settings.json hooks):<br/>shows you the steps"]
        S6 --> RESTART([Restart Claude Code<br/>to load them])
    end

    LIVE -. "shows up for everyone's next sync" .-> S1

    classDef cmd fill:#2563eb,stroke:#1e3a8a,color:#fff,font-weight:bold;
    classDef done fill:#16a34a,stroke:#14532d,color:#fff;
    classDef gate fill:#f59e0b,stroke:#92400e,color:#000;
    class PUB,SYNC cmd;
    class LIVE,INSYNC,RESTART,MERGE done;
    class Q,P1,S3 gate;
```

## When to use which

| You want to… | Command | What happens |
|--------------|---------|--------------|
| Share a skill/hook/MCP **you wrote** | `publish` (HOST) | scaffolds it into the repo, opens a PR, adds a Notion row → installable as `@slickage` once merged |
| Recommend a **third-party** tool the team should know about | `publish` (ENDORSE) | adds a Notion row pointing at its upstream install string — no hosting |
| Get the team's endorsed tools **onto your machine** | `sync` | reads the catalog, shows what you're missing, installs the ones you pick |

`publish` picks HOST vs ENDORSE automatically: `--from <local-folder>` (or a new one
you describe) → HOST; `--source <upstream-install/URL>` → ENDORSE; it asks if you
give neither.

## Prerequisites

- **Both commands** need the **Notion MCP** connected (the catalog lives in Notion).
- **`publish` HOST path** also needs `gh` authenticated + a clone of
  `slickage/claude-plugins`.
- **`sync`** also needs the `claude` CLI on your PATH.

## The one-line mental model

> **Notion is the catalog (source of truth). `publish` writes to it. `sync` reads from
> it.** Hosting in `slickage/claude-plugins` is just how *our own* tools get an install
> string; third-party tools keep theirs.
