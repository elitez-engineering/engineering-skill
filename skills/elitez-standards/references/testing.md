# Testing & Testability

We ship tested code by construction, not by hoping engineers remember. The standard is enforced by
CI gates, not goodwill.

## The test pyramid

- **Many unit tests** — pure business logic in `app/services/*` and `app/lib/*`. Fast, no I/O.
- **Some integration tests** — loaders/actions and DB access against a real (test) Postgres. Catches
  wiring and migration issues that mocks hide.
- **Few end-to-end tests** — critical user flows (register + login via Better Auth, primary
  conversion path).
- Don't invert the pyramid: if you find yourself writing an E2E test to cover logic, that logic
  probably belongs in a unit-tested service instead.

## Tooling

- **Vitest** for unit and integration tests (fast, TS-native, works with Vite/Remix).
- Test DB for integration: a real Postgres (Railway ephemeral or local container), migrated with
  drizzle-kit before the suite runs. **Do not mock the database** for logic that depends on real
  query behavior — mocked DBs pass while real migrations break.
- Playwright for E2E when a flow warrants it.

## What to test

- Every non-trivial function in `services/` gets unit tests: happy path, boundary cases, and the
  error/invalid-input path.
- Test behavior and public contracts, not private implementation details. Refactors shouldn't break
  tests that assert the same behavior.
- Bug fixes ship with a regression test that fails before the fix and passes after.

## Arranging code for testability

- Keep pure logic separate from I/O. A function that both computes and writes to the DB is hard to
  test; split "compute the change" (pure, unit-tested) from "persist it" (thin, integration-tested).
- Inject dependencies (db client, email sender, clock) as parameters rather than importing
  singletons deep in logic — this lets tests substitute a real test double at the boundary.
- Avoid reaching for global state, `Date.now()`, or `Math.random()` inside logic; pass them in.

## Test style

- Arrange–Act–Assert, one behavior per test, descriptive names: `returns 402 when subscription
  expired`.
- No logic in tests (no loops/conditionals deciding expectations) — a test should be obvious.
- Prefer real objects and fixtures over deep mock chains. If a test needs five mocks, the code under
  test is doing too much — refactor it.
- Keep tests deterministic: no reliance on wall-clock time, network, or ordering.

## CI gates (must pass to merge)

1. `tsc --noEmit` (strict, zero errors)
2. ESLint (zero errors)
3. `vitest run` (all green) with a coverage floor on `services/` (start at 80%, raise over time)
4. Drizzle migration check (schema and generated migrations are in sync)

A PR that drops coverage on changed logic or skips tests ("will add later") does not meet the
definition of done.
