# Approved Tech Stack

This is the default stack for every Elitez SaaS project. Deviating requires a written justification
in the project README (what, why, and the tradeoff). "Boring technology" is the rule; novelty is the
exception that must be argued for.

## Language & framework

- **TypeScript**, `strict: true`, everywhere (backend and frontend). No plain JavaScript in `src/`.
- **Remix** (Vite-based) as the app framework. Backend logic lives in `loader`/`action` functions
  and in framework-agnostic service modules; React handles the view.
  - Loaders/actions are thin: parse + validate input, call a service, return typed data.
  - Business logic goes in `app/services/*` (pure, testable) — not inline in loaders/actions.

## Frontend

- **React** (via Remix) + **Tailwind CSS** for styling.
- **shadcn/ui** for base components: components are copied into `app/components/ui/` and *owned* by
  the project (dependency-agnostic — we can edit them). Do not wrap them in a heavy abstraction.
- Compose small components; keep view components free of data-fetching (fetch in loaders).

## Database & migrations

- **PostgreSQL** on Railway.
- **Drizzle ORM** for schema and queries; **drizzle-kit** for migrations.
  - Schema lives in `app/db/schema.ts`. Types are inferred from the schema and reused across the app.
  - Every schema change ships a generated, committed, versioned migration. Never edit the DB by hand.
  - Run migrations in a deploy/release step, not lazily at request time.
- Derive TS types from Drizzle (`InferSelectModel` / `InferInsertModel`) — do not hand-maintain
  parallel interfaces.

## Hosting & infra (Railway)

- **Server**, **PostgreSQL**, and **object storage (bucket)** all on Railway.
- **Docker** is optional — Railway can build from source; add a Dockerfile only when the build needs
  it, and keep it minimal (multi-stage, node slim base).
- **Railway MCP** (https://docs.railway.com/ai/mcp-server) — when authorized, use the MCP server to
  inspect/manage Railway services from the agent instead of hand-copying dashboard values. This
  plugin **bundles** the Railway MCP (see `.mcp.json` at the plugin root: `railway mcp` via the
  Railway CLI), so it auto-loads when the plugin is enabled. Requires the Railway CLI installed and
  `railway login`. Treat infra actions as authorized-use only.
- Secrets come from Railway environment variables. Never commit `.env`; commit `.env.example` with
  keys and empty values only.

## Auth — Google OAuth (in code)

- Implement the OAuth 2.0 **authorization-code** flow ourselves (no heavy auth vendor).
- Store `GOOGLE_CLIENT_ID` / `GOOGLE_CLIENT_SECRET` in env; never in the repo.
- Validate the `state` parameter (CSRF), verify the ID token, and create a server-side session
  (signed, httpOnly cookie). Keep session/token handling in one `app/services/auth/*` module.
- **Auth, crypto, PII, and session handling get a Security review before merge** — loop Security in
  at design time, not after.

## Analytics — PostHog

- Product analytics + feature flags via PostHog. Initialize once (server and/or client) behind a
  small wrapper so event names are centralized (`app/lib/analytics.ts`).
- Never send PII or secrets in event properties. Gate risky rollouts behind PostHog feature flags.

## Notifications — Resend

- Transactional email via Resend. Keep templates and the send helper in `app/services/email/*`.
- Sends happen server-side (action or background job), never from the client.

## Reference project layout

```
app/
  routes/            # Remix routes (thin loaders/actions)
  services/          # framework-agnostic business logic (unit-tested)
    auth/            # Google OAuth + sessions
    email/           # Resend
  db/
    schema.ts        # Drizzle schema (source of truth for DB types)
    index.ts         # db client
  components/
    ui/              # shadcn components (owned, editable)
    <feature>/       # feature components
  lib/
    analytics.ts     # PostHog wrapper
    env.server.ts    # validated env access (zod)
drizzle/             # generated migrations (committed)
tests/               # or *.test.ts colocated with source
.env.example
drizzle.config.ts
```

## Environment variables (validate on boot)

Parse and validate all env with zod in `app/lib/env.server.ts` and fail fast at startup if a
required var is missing. Typical keys: `DATABASE_URL`, `SESSION_SECRET`, `GOOGLE_CLIENT_ID`,
`GOOGLE_CLIENT_SECRET`, `GOOGLE_REDIRECT_URI`, `POSTHOG_KEY`, `POSTHOG_HOST`, `RESEND_API_KEY`,
`RAILWAY_*` (bucket/storage credentials as provided).
