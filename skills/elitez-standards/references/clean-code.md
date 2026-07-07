# Clean Code (KISS / DRY / YAGNI in practice)

Readability is the primary feature. Code is read far more than written. Optimize for the next
engineer (or agent) who opens the file cold.

## KISS — keep it simple

- Write the simplest thing that satisfies the requirement. No speculative flexibility.
- Prefer a plain function to a class; a class to a framework; explicit code to clever code.
- If you need a comment to explain *how* clever code works, rewrite it to be obvious instead.

## YAGNI — don't build for the future

- Don't add config flags, extension points, or "just in case" parameters that no caller uses.
- Don't design generic abstractions for a single use site. Wait for the third occurrence.
- Delete dead code and unused exports rather than keeping them "in case".

## DRY — but the right kind

- Extract a shared function/component when the *same knowledge* is duplicated, not when two blocks
  merely look similar. Coincidental duplication is fine; duplicated business rules are not.
- Prefer sharing across projects (a shared component or convention) over copy-paste per project.

## Naming

- Names describe intent, not mechanism: `activeSubscribers`, not `filteredList`.
- Booleans read as predicates: `isEnabled`, `hasAccess`, `canRetry`.
- No abbreviations that aren't universal (`id`, `url`, `db` are fine; `usrCtxMgr` is not).
- Functions are verbs, values/collections are nouns.

## Functions & modules

- A function does one thing at one level of abstraction. If you narrate it with "and", split it.
- Keep functions short enough to see at once (roughly ≤ 40 lines is a smell threshold, not a law).
- Keep argument lists short (≤ 3); pass an options object when you need more.
- One module = one responsibility. Business logic lives in `app/services/*`, not in Remix
  loaders/actions or React components.
- Separate pure logic from I/O so the logic is unit-testable without heavy mocking (see testing.md).

## TypeScript rules

- `strict: true`. No `any` in committed code — use `unknown` + narrowing, or a real type.
- Types flow from the source of truth: DB types come from Drizzle; API/input types come from zod
  schemas (`z.infer`). Don't hand-maintain parallel type definitions.
- Validate all external input at the boundary (request bodies, query params, env, third-party
  responses) with zod. Inside those boundaries, trust your types.
- Prefer discriminated unions and exhaustive `switch` over boolean flags for state.
- No non-null `!` assertions to silence the compiler — handle the null case.

## Error handling

- Validate at system boundaries only (user input, external APIs, env). Don't defensively re-check
  internal invariants the type system already guarantees.
- Fail fast and loud on programmer errors; handle and surface user-facing errors cleanly.
- Never swallow errors silently (`catch {}`). Log with context or rethrow. Silent failures are a
  bug we specifically guard against.
- Return typed results or throw typed errors; don't return `null` to mean three different things.

## Comments

- Default to no comments — good names and small functions carry the meaning.
- Write a comment only when the *why* is non-obvious: a hidden constraint, a subtle invariant, a
  workaround for a specific bug, or behavior that would surprise a reader.
- Don't describe *what* the code does, and don't reference the task/ticket/PR — that rots.

## Formatting & tooling

- Prettier for formatting, ESLint (typescript-eslint) for lint. Both run in CI and must pass.
- No commented-out code and no `console.log` left in committed code (use the logger/analytics
  wrapper). No `// TODO` without an owner or tracked issue.
