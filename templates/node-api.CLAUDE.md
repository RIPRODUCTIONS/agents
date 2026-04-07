# CLAUDE.md — Node.js API Project

## Stack

Node.js, Express, TypeScript, PostgreSQL, Prisma ORM, JWT auth (access + refresh tokens), Redis, deployed on Railway.

## Folder structure

```
src/
├── routes/           ← route definitions grouped by resource
│   ├── auth.ts
│   ├── users.ts
│   └── index.ts      ← mounts all routers
├── middleware/        ← auth, validation, rate limiting, error handler
├── controllers/      ← request handling — parse input, call service, send response
├── services/         ← business logic — the only layer that touches Prisma
├── lib/              ← Prisma client, Redis client, JWT helpers, logger
├── types/            ← shared TypeScript types and interfaces
└── index.ts          ← app bootstrap and server start
```

## Route conventions

- RESTful resources prefixed with `/api/v1`. One file per resource in `src/routes/`.
- Validation via Zod schemas in middleware before the controller runs.

## Middleware order

```
cors → helmet → rate-limit → json-parser → auth → validation → controller → error-handler
```

## Error handling

- Throw typed `AppError` (with `statusCode` and `code`). Global handler catches all, logs full error, returns sanitized JSON.
- Format: `{ "error": { "code": "NOT_FOUND", "message": "User not found" } }`
- Never expose stack traces or internal details in responses.

## Auth

- Access token: short-lived JWT (15 min), sent in `Authorization: Bearer` header.
- Refresh token: long-lived, stored in Redis with user ID, rotated on use.
- Auth middleware in `src/middleware/auth.ts` — validates token, attaches `req.user`.
- Password hashing: bcrypt with cost factor 12.

## Data access

All DB access through Prisma in `src/services/` — controllers never import Prisma. Single client in `src/lib/db.ts`. Redis for sessions, rate limiting, and caching via `src/lib/redis.ts`.

## Testing

- Framework: **Jest** + **supertest** for HTTP tests
- Test files: `__tests__/[name].test.ts` colocated with source
- Run: `npx jest` (watch) / `npx jest --ci` (CI)
- Minimum: test all routes, auth flows, and error cases

## Commands

```bash
npx prisma migrate dev   # run migrations
npm run dev              # dev server (port 3000)
npm run build && npm start  # production
npx jest --ci            # run tests
```
