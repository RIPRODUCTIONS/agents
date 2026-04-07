# CLAUDE.md — Next.js Project

## Stack

Next.js 15 (App Router), TypeScript (strict), Tailwind CSS, PostgreSQL, Prisma ORM, Supabase Auth, Redis, deployed on Vercel.

## Folder structure

```
src/
├── app/              ← routes, layouts, loading/error boundaries
│   ├── (auth)/       ← grouped auth routes (login, signup, reset)
│   ├── (dashboard)/  ← protected routes
│   └── api/          ← route handlers
├── components/
│   ├── ui/           ← shared primitives (Button, Input, Modal)
│   └── [feature]/    ← feature-scoped components
├── lib/              ← shared utilities, Prisma client, Redis client
├── actions/          ← server actions
└── types/            ← shared TypeScript types
```

## Naming conventions

- Files and folders: `kebab-case` (`user-profile.tsx`)
- Components: `PascalCase` (`UserProfile`)
- Utilities/hooks: `camelCase` (`useAuth`, `formatDate`)
- Database tables: `snake_case` via Prisma (`user_sessions`)
- Env vars: `UPPER_SNAKE_CASE` (`DATABASE_URL`)

## Component patterns

- Server Components by default. Add `"use client"` only when needed (state, effects, browser APIs).
- Colocate loading.tsx and error.tsx with their route segments.
- Extract forms into client components; keep pages as server components.
- Use server actions for mutations. Route handlers only for webhooks/external APIs.

## Auth

Supabase Auth handles sessions. Protect routes via middleware in `src/middleware.ts`. Never trust client-side auth checks alone — validate on the server.

## Data access

- All DB access through Prisma. Single client instance in `src/lib/db.ts`.
- Redis for caching and rate limiting via `src/lib/redis.ts`.
- Never import Prisma directly in components — use server actions or `lib/` functions.

## Testing

- Framework: **Vitest** + **React Testing Library**
- Test files: colocated as `[name].test.ts(x)` next to source
- Run: `npx vitest` (watch) / `npx vitest run` (CI)
- Minimum: test all server actions, API route handlers, and auth flows

## Commands

```bash
npx prisma migrate dev    # run migrations
npx prisma generate       # regenerate client after schema change
npm run dev               # start dev server (port 3000)
npm run build             # production build
npx vitest run            # run tests
```
