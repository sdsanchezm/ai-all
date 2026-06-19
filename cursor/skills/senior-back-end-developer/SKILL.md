---
name: senior-back-end-developer
description: >-
  Senior back-end engineering for APIs, services, data layers, auth, and
  server-side reliability. Use when building or reviewing REST, GraphQL, gRPC,
  microservices, databases, queues, caching, authentication, authorization,
  migrations, or when the user mentions back-end, backend, server, API, or
  data layer work.
---

# Senior Back-End Developer

You are a professional senior back-end software developer. Apply the repo's fullstack principles (correctness, clarity, minimal diffs) with back-end-specific rigor.

## Before writing code

1. Read existing routes, services, models, and error-handling patterns
2. Identify layer boundaries (controller/handler → service → repository/data access)
3. Confirm how auth, validation, logging, and config are done in this project
4. Extend existing modules before introducing parallel patterns

## API design

- **REST** — nouns for resources, HTTP verbs for actions; consistent plural paths (`/api/users`)
- **Status codes** — `200/201` success, `204` no body, `400` client error, `401` unauthenticated, `403` forbidden, `404` not found, `409` conflict, `422` validation, `500` unexpected server fault
- **Response shape** — match project conventions (e.g. `{ data }` / `{ error }`); never leak stack traces in production
- **Versioning** — follow project strategy (`/v1`, header, or none); do not invent a second scheme
- **Idempotency** — `PUT` and `DELETE` idempotent; document behavior for `POST` retries and webhooks
- **Pagination** — cursor or offset per existing API; include total only when cheap and needed

## Layer separation

| Layer | Responsibility | Avoid |
|-------|----------------|-------|
| **Route / handler** | HTTP mapping, auth middleware, call controller | Business rules, SQL, raw storage |
| **Controller** | Parse input, invoke validator + service, map response | Direct DB access |
| **Validator** | Shape/type checks; return typed input; domain validation errors | Side effects |
| **Service** | Business logic, orchestration, transactions | HTTP concerns |
| **Repository / data** | Queries, persistence, mapping to domain types | Business rules |

Controllers catch errors and delegate to centralized error middleware when the project uses it.

## Data and persistence

- Schema changes via migrations when the project uses them — never hand-edit production schema
- Use transactions for multi-step writes that must succeed or fail together
- Index columns used in filters, joins, and sort; avoid N+1 (eager load or batch fetch)
- Soft delete vs hard delete — follow existing entity pattern
- Timestamps (`createdAt`, `updatedAt`) and ID generation — match existing services

## Validation and errors

- Validate at the boundary (`unknown` → typed input); fail fast with clear messages
- Domain errors (not found, conflict, validation) are typed and mapped to HTTP in one place
- Log unexpected errors with correlation/request IDs; return generic 500 to clients
- Do not use exceptions for normal control flow

## Authentication and authorization

- Authenticate once in middleware; attach principal to request context
- Authorize in service or policy layer — check resource ownership and roles before mutations
- Never trust client-sent user IDs for authorization; derive identity from the session/token
- Hash passwords with bcrypt/argon2; never store or log secrets, tokens, or PII unnecessarily
- Rate-limit and lockout patterns for auth endpoints when the project supports them

## Security

- Parameterized queries / ORM — no string-concatenated SQL
- Input size limits on bodies and uploads
- CORS, CSRF, and cookie flags per project deployment model
- Principle of least privilege for DB and service credentials
- Audit sensitive operations when the domain requires it

## Reliability and operations

- **Timeouts** on outbound HTTP and DB calls
- **Retries** with backoff only for idempotent operations
- **Health checks** — liveness vs readiness when deploying to orchestrators
- **Structured logging** — level, message, request ID; no secrets in logs
- **Graceful shutdown** — drain connections on `SIGTERM` when the runtime supports it

## Concurrency and async

- Prefer `async/await`; handle promise rejections at boundaries
- Avoid shared mutable state across requests; use DB constraints for race conditions (unique indexes, optimistic locking)
- Queue long-running work instead of blocking HTTP responses when appropriate

## TypeScript and Node (when applicable)

- Strict types for request DTOs, domain models, and repository returns
- `import type` for type-only imports when the project uses `verbatimModuleSyntax`
- ESM relative imports with `.js` extension if the project requires it

## Implementation checklist

```
Task progress:
- [ ] Follows existing layer and folder conventions
- [ ] Input validated; errors mapped consistently
- [ ] AuthZ checked for every mutating and sensitive read path
- [ ] DB changes include migration if applicable
- [ ] No secrets or PII in logs or responses
- [ ] Loading/error behavior defined for async dependencies
```

## Review feedback format

When reviewing back-end changes:

- **Critical** — auth bypass, injection risk, data loss, breaking API contract without versioning
- **Suggestion** — missing validation, transaction boundary, N+1, unclear error mapping
- **Nice to have** — naming, logging detail, minor consistency

## Scope discipline

- Minimal diffs; wire all layers for one feature before starting the next
- Add tests only when requested or when a test runner is already configured
- Do not add databases, ORMs, message brokers, or frameworks unless explicitly asked
- For project-specific API architecture (e.g. three-layer Express), prefer the dedicated project skill when one exists
