---
description: Review the current changes (or a given path) against Elitez Engineering standards — stack, clean code/KISS, types, testing, security, reusability.
argument-hint: "[path-or-diff-scope]"
---

Review code against the Elitez Engineering standards. First read the `elitez-standards` skill and
its reference files so your feedback cites concrete rules.

Scope: **$1** (default to the current git diff vs the base branch if empty; if not a git repo, review
the files the user points at).

Check the change against, at minimum:

- **Stack** — uses approved stack; any deviation is justified in the README.
- **Types** — strict TS, no `any`, no `!` silencing; types derived from Drizzle/zod not duplicated.
- **Structure** — thin loaders/actions, logic in `services/*`, presentational components (no DB/
  service calls in React).
- **Simplicity** — KISS/YAGNI: no speculative abstraction, no dead code, no over-engineering.
- **Validation & errors** — input validated at boundaries with zod; no swallowed errors / silent
  failures.
- **Database** — schema changes ship a committed Drizzle migration; schema and migrations in sync.
- **Security** — no committed secrets; Better Auth owns sessions/CSRF/hashing/provider token
  verification (not hand-rolled); no PII in PostHog or Sentry; server-side-only email. Flag anything
  touching auth/crypto/PII/session for Security review.
- **Testing** — new service logic has unit tests (happy/boundary/error); no failing/skipped tests;
  no dropped coverage; DB not mocked where real query behavior matters.
- **Reuse & components** — shared code not copy-pasted; composition over configuration; accessible,
  typed props.
- **Hygiene** — Prettier/ESLint clean; no `console.log`/commented-out code/ownerless TODOs.

Output a concise report grouped as **Blocking**, **Should-fix**, and **Nits**. For each finding give:
the file:line, the standard it violates (cite the reference file), and the concrete fix. End with a
clear verdict: approve, approve-with-nits, or changes-requested. Be specific — name the risk, name
the fix. Do not rubber-stamp.
