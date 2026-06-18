---
name: three-layer-api
description: >-
  Implements and extends the Express + TypeScript three-layer API architecture
  in api_3layer_v1. Use when adding endpoints, resources, controllers, services,
  validators, routes, error types, or when the user mentions three-layer,
  layered API, Express REST, or CRUD in this project.
---

# Three-Layer API (api_3layer_v1)

Express 5 REST API with strict TypeScript, ESM, and a fixed layer separation. Follow existing `items` and `health` modules as the canonical pattern.

## Architecture

```
HTTP request
  → routes/       (path + HTTP verb → controller)
  → controllers/  (parse params, call validator + service, send response)
  → validators/   (shape/type checks on request body → typed input)
  → services/     (business logic, storage, domain errors)
  → models/       (interfaces only — no runtime code)
```

Supporting layers:

- `middleware/errorHandler.ts` — maps domain errors to HTTP status codes
- `config/env.ts` — reads `PORT` and `NODE_ENV` from `.env`
- `routes/index.ts` — mounts feature routers under `/api`

## Layer rules

| Layer | Does | Does not |
|-------|------|----------|
| **routes** | Wire verbs/paths to controller functions | Validate, access storage, format JSON bodies |
| **controllers** | Extract params, call validator/service, set status + JSON | Contain business rules or direct storage |
| **validators** | Validate `unknown` input, return typed model input, throw `ValidationError` | Touch services or `res` |
| **services** | CRUD, in-memory `Map` storage, throw `NotFoundError` | Know about Express `Request`/`Response` |
| **models** | Export `interface` types for entity + create/update input | Export classes or functions |

## Response conventions

- Success with body: `{ data: <payload> }` with `200` or `201`
- Delete success: `204` with empty body
- Client error: `{ error: "<message>" }` with `400` (validation) or `404` (not found)
- Server error: `{ error: "Internal server error" }` with `500`
- Health check is the exception — returns `{ status, environment, timestamp }` directly

## Error handling

1. Define domain errors in the layer that owns the concern:
   - `ValidationError` in `validators/<resource>Validator.ts`
   - `NotFoundError` in `services/<resource>Service.ts` (reuse existing class when possible)
2. Services throw errors; controllers catch and call `next(error)`
3. Register new error types in `middleware/errorHandler.ts` before the 500 fallback

Controller pattern:

```typescript
export function createThing(req: Request, res: Response, next: NextFunction): void {
  try {
    const input = validateCreateThingInput(req.body);
    const thing = thingService.createThing(input);
    res.status(201).json({ data: thing });
  } catch (error) {
    next(error);
  }
}
```

## TypeScript & ESM

- `"type": "module"` — use `.js` extensions in all relative imports (`../models/item.js`)
- `verbatimModuleSyntax: true` — use `import type` for type-only imports
- `exactOptionalPropertyTypes: true` — assign optional fields only when defined; do not set `undefined`
- Strict mode is on — no `any`, handle `unknown` in validators

## Add a new resource (checklist)

Copy the `items` module. Replace `Item` / `item` with the new resource name.

```
Task progress:
- [ ] 1. models/<resource>.ts        — Item, CreateItemInput, UpdateItemInput interfaces
- [ ] 2. validators/<resource>Validator.ts — validateCreate/Update functions
- [ ] 3. services/<resource>Service.ts   — CRUD + in-memory Map + NotFoundError
- [ ] 4. controllers/<resource>Controller.ts — list, get, create, update, remove handlers
- [ ] 5. routes/<resource>Routes.ts        — REST routes on a Router
- [ ] 6. routes/index.ts                   — mount at `/api/<plural>`
- [ ] 7. errorHandler.ts                   — map new error classes if any
```

### Model template

```typescript
export interface Thing {
  id: string;
  name: string;
  createdAt: Date;
  updatedAt: Date;
}

export interface CreateThingInput {
  name: string;
}

export interface UpdateThingInput {
  name?: string;
}
```

### Service conventions

- Generate IDs with `crypto.randomUUID()`
- Set `createdAt` and `updatedAt` on create; update `updatedAt` on patch
- Use partial-update pattern: spread existing entity, apply only defined input fields
- Export plain functions (not a class) matching `itemService.ts`

### Route conventions

- Mount plural nouns: `/api/items`, `/api/things`
- Standard REST mapping: `GET /`, `GET /:id`, `POST /`, `PUT /:id`, `DELETE /:id`
- Validate `req.params.id` in a local `getIdParam` helper (string, non-empty)

## Commands

```bash
npm run dev      # nodemon + ts-node (ESM)
npm run build    # tsc → dist/
npm start        # node dist/index.js
```

## Scope discipline

- Match existing file and naming conventions before introducing new abstractions
- Do not add a database, ORM, or validation library unless explicitly requested
- Do not add tests unless requested (no test runner is configured yet)
- Keep changes minimal — one resource at a time, all layers wired before moving on
