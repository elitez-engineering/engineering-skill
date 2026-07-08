---
description: Scaffold a new Elitez SaaS project on the approved stack (Remix + TypeScript, React/Tailwind/shadcn, Drizzle + PostgreSQL, Better Auth for login/register, Sentry, PostHog, Resend, Railway).
argument-hint: "[project-name]"
---

You are scaffolding a new Elitez project. First read the `elitez-standards` skill (especially
`references/tech-stack.md`) and follow it exactly. Do not invent an alternative stack.

Project name: **$1** (ask the user if empty).

Produce a minimal, working, standards-compliant starting point — KISS, no speculative features:

1. **Confirm intent** — one short question round only if something is genuinely ambiguous (e.g.
   package manager). Otherwise proceed with sensible defaults.
2. **Init Remix + Vite + TypeScript** project with `strict: true` tsconfig.
3. **Tailwind + shadcn/ui** configured; add `app/components/ui/` with a `Button` as the seed
   component. Components are owned in-repo (dependency-agnostic).
4. **Drizzle + PostgreSQL**: `drizzle.config.ts`, `app/db/schema.ts` (with an example `users` table),
   `app/db/index.ts` client, and an initial generated migration under `drizzle/`.
5. **Env validation**: `app/lib/env.server.ts` using zod; fail fast on missing vars. Add
   `.env.example` (keys only, no values) and ensure `.env` is gitignored.
6. **Auth**: configure **Better Auth** in `app/services/auth/` with the Drizzle adapter (against our
   Postgres) for **login AND register** — enable email/password and Google as a social provider.
   Let Better Auth own sessions, CSRF, password hashing, and token verification; add its
   user/session/account tables via a Drizzle migration. Mark auth/crypto/session for Security review
   before merge.
7. **Integrations**: `app/lib/analytics.ts` (PostHog wrapper — product analytics only),
   `app/lib/monitoring.ts` (Sentry wrapper — error tracking + tracing, server + client), and
   `app/services/email/` (Resend helper) — server-side sends only.
8. **Tooling**: ESLint (typescript-eslint) + Prettier + Vitest configured. Add one example unit test
   for a `services/` function and wire `test`, `lint`, `typecheck` scripts.
9. **Railway**: note in the README how to deploy (server + Postgres + bucket), that Docker is
   optional, and that the Railway MCP (https://docs.railway.com/ai/mcp-server) can manage services.
   Migrations run in a release step, not at request time.
10. **README** documenting the stack, env vars, how to run/test/migrate, and any deviation
    justification (there should be none by default).

After scaffolding: run typecheck, lint, and the test suite; report results. Do not commit secrets.
End with a short summary of what was created and the exact next commands to run the app.
