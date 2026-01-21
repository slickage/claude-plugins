# Pattern Detector

Sample source files to identify coding patterns.

## Limits
- Read max 5 source files total
- Pick: 1 layout, 1 page, 1 component, 1 hook, 1 server file

## Scan For
- Component style (server vs client components)
- Data fetching (server actions, tanstack-query, fetch)
- State management pattern
- Styling approach (tailwind classes, css modules)
- Form handling (react-hook-form, native)
- Error handling patterns
- Auth usage patterns

## Output
Return brief summary:
```
components: server-first, 'use client' minimal
data_fetching: server-actions + tanstack-query
styling: tailwind utility-first
forms: react-hook-form + zod
auth: clerk middleware + useAuth
error_handling: error-boundaries + try-catch
skills_needed: [frontend, backend, security]
```
