---
name: elitez-standards
description: Elitez Engineering's shared standards for building SaaS web apps. Use whenever you write, review, scaffold, or refactor code in an Elitez project — it defines the approved tech stack (Remix + TypeScript, React/Tailwind/shadcn, Drizzle, PostgreSQL on Railway, Google OAuth, PostHog, Resend), plus clean-code/KISS rules, testability and unit-test conventions, reusable-component patterns, and explicit do/don't guardrails. Read the matching reference file before making a decision the standard covers.
---

# Elitez Engineering Standards

The single source of truth for how Elitez builds web SaaS. The goal: every project stands on
scalable, documented, tested foundations, and each new project is cheaper to start than the last
because we reuse standards instead of re-deciding.

Apply these standards when you scaffold a project, add a feature, review a PR, or refactor. When a
decision is covered by a reference file below, read that file first and follow it. When it is not
covered, fall back to the principles in "Core principles" and leave the codebase more consistent
than you found it, not less.

## When to use this skill

- Starting a new Elitez project or feature.
- Writing or reviewing backend (Remix loaders/actions, services), frontend (React components), or
  database (Drizzle schema/migrations) code.
- Deciding between two approaches and wanting the house style / approved dependency.
- Doing a code review and needing a concrete, citable standard.

## Approved stack (summary)

| Layer            | Choice                                              | Why |
| ---------------- | --------------------------------------------------- | --- |
| Language         | TypeScript (strict) end-to-end                      | One language BE+FE, fast AI-assisted dev, strong types |
| App framework    | Remix (TypeScript BE + React FE)                    | Unified loaders/actions, progressive enhancement, web-standards |
| UI               | React + Tailwind CSS + shadcn/ui                    | Dependency-agnostic, copy-in components you own |
| ORM / migrations | Drizzle ORM + drizzle-kit                           | Type-safe SQL, explicit versioned migrations |
| Database         | PostgreSQL (Railway)                                | Boring, proven, relational integrity |
| Hosting          | Railway (server + Postgres + bucket)                | One platform, simple deploys, optional Docker |
| Auth             | Google OAuth (authorization-code flow, in code)     | No heavy auth vendor lock-in; owned in-repo |
| Analytics        | PostHog                                             | Product analytics + feature flags |
| Email/notify     | Resend                                              | Simple transactional email API |
| Infra assistant  | Railway MCP (https://docs.railway.com/ai/mcp-server)| Manage Railway from the agent when authorized |

Full rationale, versions, and configuration guidance: **references/tech-stack.md**.

## Core principles

1. **KISS** — the simplest design that satisfies the requirement wins. No speculative abstraction.
2. **YAGNI** — don't build for hypothetical futures. Three similar lines beat a premature helper.
3. **DRY across projects, not just within one** — prefer a shared component/convention over a
   bespoke per-project solution.
4. **Boring technology** — prefer proven, documented tech; novelty needs a written justification.
5. **Type-safety is non-negotiable** — `strict` TypeScript, no `any` in committed code, types flow
   from Drizzle schema to the UI.
6. **Testable by construction** — pure logic separated from I/O so it can be unit-tested without
   mocks-of-everything. See references/testing.md.
7. **Small blast radius** — a failure in one module/route should not cascade. Validate at boundaries.
8. **Total cost of ownership** — optimize for maintenance, onboarding, and readability, not just
   time-to-first-demo.

## Reference files (read the one that matches your task)

- **references/tech-stack.md** — approved stack, versions, project layout, Railway + Drizzle +
  Google OAuth + PostHog + Resend setup.
- **references/clean-code.md** — KISS/DRY/YAGNI in practice, naming, function/module size, error
  handling at boundaries, comments policy, TypeScript strictness rules.
- **references/testing.md** — the test pyramid, what to unit-test vs integration-test, Vitest
  conventions, arranging code for testability, CI gates.
- **references/reusable-components.md** — how we build shareable React/shadcn components and shared
  TS packages: props contracts, accessibility, composition over configuration.
- **references/do-and-dont.md** — a fast checklist of concrete do's and don'ts to scan before a PR.

## Definition of done (engineering)

A change is done when: it compiles under strict TS with no `any`; lint passes; unit tests cover the
new logic and the suite is green; it follows the approved stack and the relevant reference file;
inputs are validated at boundaries; and no secret is committed. If it introduces a pattern other
projects will want, it is extracted or documented so the next project reuses it.
