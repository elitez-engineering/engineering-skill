# Do & Don't — Fast Checklist

Scan this before opening a PR or approving one. Each line is a concrete, citable rule.

## Stack

- ✅ Use the approved stack: TypeScript, Remix, React + Tailwind + shadcn, Drizzle, PostgreSQL on
  Railway, Google OAuth, PostHog, Resend.
- ✅ Justify any deviation in the project README (what, why, tradeoff).
- ❌ Don't add a new dependency when the stack already covers the need.
- ❌ Don't introduce novel/unproven tech without a written reason.

## TypeScript & types

- ✅ `strict: true`; derive DB types from Drizzle and input types from zod (`z.infer`).
- ❌ No `any` in committed code. No `!` non-null assertions to silence the compiler.
- ❌ Don't hand-maintain types that duplicate the schema.

## Structure

- ✅ Keep loaders/actions thin; put business logic in `services/*`.
- ✅ Keep view components presentational; fetch data in loaders.
- ❌ Don't call the DB or services directly from React components.
- ❌ Don't mix business logic into route files.

## Simplicity

- ✅ KISS/YAGNI: simplest thing that works; wait for the third occurrence before abstracting.
- ❌ Don't add speculative config, flags, or extension points nothing uses.
- ❌ Don't over-engineer a one-off into a framework.

## Validation & errors

- ✅ Validate all external input at the boundary with zod; validate env on boot.
- ✅ Surface errors with context; log meaningfully.
- ❌ Never swallow errors (`catch {}`). No silent failures.
- ❌ Don't defensively re-validate internal invariants the types already guarantee.

## Database & migrations

- ✅ Every schema change ships a generated, committed Drizzle migration.
- ✅ Run migrations in a release step; keep schema and migrations in sync (CI checks this).
- ❌ Don't edit the database by hand or mutate schema without a migration.

## Security & secrets

- ✅ Secrets in Railway env vars; commit only `.env.example` with empty values.
- ✅ Google OAuth: validate `state`, verify the ID token, use signed httpOnly session cookies.
- ✅ Route auth/crypto/PII/session changes through Security review before merge.
- ❌ Never commit secrets, tokens, or credentials. Never send PII in PostHog events.
- ❌ Don't send email from the client — Resend sends are server-side only.

## Testing

- ✅ Unit-test service logic (happy, boundary, error paths); ship regression tests with bug fixes.
- ✅ Integration-test against a real migrated test Postgres.
- ❌ Don't mock the database for logic that depends on real query behavior.
- ❌ Don't merge with failing/skipped tests or dropped coverage on changed logic.

## Reuse & components

- ✅ Prefer shared components/helpers; extract on the third occurrence; document shared packages.
- ✅ Composition over configuration; accessible, typed props.
- ❌ Don't copy-paste cross-project code that belongs in a shared package.
- ❌ Don't build a 15-prop mega-component.

## Hygiene

- ✅ Prettier + ESLint pass in CI; small, focused PRs.
- ❌ No commented-out code, stray `console.log`, or ownerless `// TODO` in committed code.
- ❌ Don't bypass CI gates, signing, or review to ship faster.
