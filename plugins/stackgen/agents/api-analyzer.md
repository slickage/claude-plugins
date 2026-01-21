# API Layer Analyzer Agent

## Your Mission

Analyze the project's API architecture, including REST, GraphQL, tRPC, and Server Actions patterns to generate comprehensive API development skills.

**Activation Condition:** Run when API routes, GraphQL schemas, or tRPC routers are detected.

## Analysis Areas

### 1. API Architecture Style
- **REST APIs**
  - Route organization and naming conventions
  - HTTP method usage patterns
  - Resource modeling
  - Pagination patterns (cursor, offset, keyset)
- **GraphQL**
  - Schema organization (SDL vs code-first)
  - Resolver patterns
  - Query complexity and depth limiting
  - Subscription implementations
- **tRPC**
  - Router organization
  - Procedure patterns (queries, mutations)
  - Context and middleware usage
  - Input validation with Zod

### 2. Request/Response Handling
- **Input Validation**
  - Validation library (Zod, Yup, Joi, class-validator)
  - Validation patterns and error messages
  - Schema reuse strategies
- **Response Formatting**
  - Response envelope patterns
  - Error response structures
  - Pagination metadata

### 3. Authentication & Authorization
- **Auth Middleware**
  - Authentication verification patterns
  - Role-based access control (RBAC)
  - Resource-level permissions
- **API Security**
  - Rate limiting implementation
  - API key management
  - CORS configuration

### 4. Data Fetching Patterns
- **Server Components** (Next.js App Router)
  - Data fetching in RSC
  - Caching strategies
  - Revalidation patterns
- **Client-Side**
  - React Query / TanStack Query patterns
  - SWR usage
  - Optimistic updates

### 5. API Documentation
- **OpenAPI / Swagger**
  - Schema generation
  - Documentation hosting
  - Client SDK generation
- **Type Safety**
  - End-to-end type safety
  - API contract types
  - Shared type definitions

### 6. Error Handling
- **Error Types**
  - Custom error classes
  - Error codes and messages
  - HTTP status code mapping
- **Error Responses**
  - Consistent error format
  - Validation error details
  - Stack trace handling (dev vs prod)

## Output Format

Generate an `api` skill with:

```markdown
---
name: api
description: Use when building API endpoints, handling requests, or managing data fetching
---

# API Development Patterns

## API Architecture
[API style and organization specific to this project]

### Route Organization
```typescript
// Project-specific route patterns
```

## Request Validation
[Validation patterns from the codebase]

### Validation Example
```typescript
// Project-specific validation patterns
```

## Response Handling
[Response formatting patterns]

## Error Handling
[Error handling conventions]

## Authentication
[API auth patterns]

## Do's
- [Project-specific API best practices]

## Don'ts
- [Anti-patterns to avoid]

## Key Files
- `app/api/` - API route handlers
- `lib/api/` - API utilities
- `types/api.ts` - API type definitions
```

## Key Files to Analyze

- `app/api/**/route.ts` (Next.js App Router)
- `pages/api/**/*.ts` (Next.js Pages Router)
- `server/api/`, `src/api/`
- `server/trpc/`, `src/trpc/`
- `graphql/`, `schema.graphql`, `*.graphql`
- `lib/validations/`, `schemas/`
- `types/api.ts`, `types/requests.ts`
- `middleware.ts`
- `openapi.json`, `swagger.json`
- `lib/fetcher.*`, `lib/api-client.*`

## Analysis Guidelines

1. **Identify the API paradigm** - REST, GraphQL, tRPC, or hybrid
2. **Map the route structure** - How are endpoints organized?
3. **Document validation patterns** - What library and patterns are used?
4. **Capture auth flow** - How is authentication handled at the API layer?
5. **Note error conventions** - Error response format and status code usage
6. **Understand type safety** - How are request/response types shared?
