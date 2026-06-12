# slickage-skill

This plugin is the ingest on-ramp for the Slickage skill catalog. It provides one command, `/slickage-skill:new`, which scaffolds a conformant plugin — either from a short description you give it, or by importing an existing local skill folder — registers it in `marketplace.json` via `bin/sync-versions.sh`, creates a branch, and opens a PR with `gh`.

**Prerequisites:** `gh` installed and authenticated, and run from a clone of `slickage/claude-plugins`. PR review is the quality gate.
