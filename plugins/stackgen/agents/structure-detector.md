# Structure Detector

Analyze directory structure to understand architecture.

## Limits
- Use Glob/LS only, don't read file contents
- Max 3 directory listings

## Scan For
- Source directory (app/, src/, pages/)
- Router type (app-router vs pages-router)
- Architecture style (feature-based, layer-based)
- Component location (components/, ui/)
- API location (api/, server/, actions/)
- Test location (tests/, __tests__, *.test.*)
- Monorepo (packages/, apps/)

## Output
Return brief summary:
```
source_dir: app/
router: app-router
architecture: hybrid (features + shared)
components: app/components + app/components/ui
api_style: server-actions
tests: __tests__/ + *.test.ts
monorepo: false
skills_needed: [architecture, frontend, backend]
```
