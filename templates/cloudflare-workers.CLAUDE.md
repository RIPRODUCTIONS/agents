# CLAUDE.md — Cloudflare Workers Project

## Stack

Cloudflare Workers, TypeScript, Hono (lightweight web framework),
Cloudflare D1 (SQLite database), Cloudflare KV (key-value store),
Cloudflare R2 (object storage if needed), deployed via Wrangler CLI.

## Why Cloudflare Workers

- Runs at the edge — closest server to every user globally
- Zero cold starts unlike AWS Lambda
- D1 database is SQLite, simple and fast for most use cases
- KV store replaces Redis for caching and session storage
- Free tier is generous — 100,000 requests per day
- Wrangler CLI makes deploy a single command

## Folder structure

```
src/
├── index.ts          ← Worker entry point, Hono app, route mounts
├── routes/           ← one file per resource (auth.ts, game.ts, etc.)
├── middleware/        ← auth, rate limiting, CORS
├── services/         ← business logic — the only layer that touches D1 or KV
├── types/            ← shared TypeScript types and interfaces
└── utils/            ← helpers (jwt.ts, hash.ts, response.ts)
wrangler.toml         ← Cloudflare Workers config (bindings, routes, env)
```

## wrangler.toml structure

```toml
name = "your-project-name"
main = "src/index.ts"
compatibility_date = "2024-01-01"

[[d1_databases]]
binding = "DB"
database_name = "your-db-name"
database_id = "your-database-id"

[[kv_namespaces]]
binding = "KV"
id = "your-kv-id"

[vars]
ALLOWED_ORIGINS = "https://your-frontend.vercel.app"

# Secrets set via: wrangler secret put JWT_SECRET
# Never put secrets in wrangler.toml
```

## Naming conventions

- Files and folders: kebab-case (game-state.ts)
- Classes and types: PascalCase (GameState, UserService)
- Functions and variables: camelCase (getCurrentUser)
- Database tables: snake_case plural (game_states, attack_logs)
- KV keys: colon-separated (session:userId, game:gameId)
- Env vars and secrets: UPPER_SNAKE_CASE (JWT_SECRET)

## Route conventions

- All routes prefixed with /api/v1
- One file per resource in src/routes/
- Routes mounted in src/index.ts via Hono
- Zod validates all input — never trust raw request body

## Middleware order

```
CORS → rate-limit (KV-based) → auth → validation (Zod) → handler → error-handler
```

## Auth

- JWT access tokens, short-lived (15 min)
- Refresh tokens stored in KV with TTL, rotated on use
- getCurrentUser middleware injected into protected routes
- Password hashing: bcrypt (use crypto.subtle for Workers compatibility)
- Never trust client-side claims — always validate server-side

## Data access

- All D1 access through services/ — routes never query D1 directly
- Use prepared statements always — never string-concatenate SQL
- KV for sessions, rate limiting, and caching
- Single DB and KV binding passed via Hono context

## Error handling

- Return typed error responses always
- Format: {"error": {"code": "NOT_FOUND", "message": "Game not found"}}
- Never expose internal details or stack traces in responses
- Log errors to Cloudflare dashboard via console.error

## WebSocket (Durable Objects)

For real-time features (game ticks, live updates):
```toml
# Add to wrangler.toml
[[durable_objects.bindings]]
name = "GAME_SESSION"
class_name = "GameSession"
```
Durable Objects handle persistent WebSocket connections and stateful game simulation.

## Testing

- Framework: Vitest + @cloudflare/vitest-pool-workers
- Test files: src/routes/[name].test.ts
- Run: npx vitest (watch) / npx vitest run (CI)
- Minimum: test all routes, auth flows, D1 queries, KV operations

## Commands

```bash
wrangler dev                          # local dev server (port 8787)
wrangler deploy                       # deploy to Cloudflare Workers
wrangler d1 execute DB --local --file=schema.sql   # run migrations locally
wrangler d1 execute DB --file=schema.sql           # run migrations in production
wrangler secret put JWT_SECRET        # set a secret (never in wrangler.toml)
wrangler tail                         # live logs from production
npx vitest run                        # run tests
```

## Environment variables

Secrets set via wrangler CLI — never in wrangler.toml or committed to git:
```bash
wrangler secret put JWT_SECRET
wrangler secret put DATABASE_ENCRYPTION_KEY
```

Non-secret config goes in wrangler.toml under [vars]:
```toml
[vars]
ALLOWED_ORIGINS = "https://your-app.vercel.app"
ENVIRONMENT = "production"
```

## Deployment

```bash
wrangler deploy
```

Your worker is live at: https://your-project-name.your-subdomain.workers.dev
Custom domain via Cloudflare dashboard (DNS already on Cloudflare — one click).
```
