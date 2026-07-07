# Elitez Engineering — Claude Code Plugin

A shareable [Claude Code](https://docs.claude.com/en/docs/claude-code) plugin that encodes Elitez's
engineering standards so every project starts from the same scalable, documented, tested foundation.
It bundles the approved SaaS tech stack, clean-code / KISS rules, testability and unit-test
conventions, reusable-component patterns, and explicit do/don't guardrails.

## What's inside

- **Skill: `elitez-standards`** — the source of truth. Auto-loaded when you write, review, scaffold,
  or refactor Elitez code. Covers the stack, clean code, testing, reusable components, and do/don'ts
  via focused reference files.
- **Commands**
  - `/elitez-scaffold [name]` — scaffold a new project on the approved stack.
  - `/elitez-review [scope]` — review changes against the standards, with a Blocking/Should-fix/Nit
    verdict.
  - `/elitez-component <Name> [intent]` — generate a reusable, accessible, typed React/shadcn
    component with a colocated test.
- **Agent: `elitez-reviewer`** — a subagent that reviews code against the standards; invoke it
  proactively after a feature or before a merge.

## The approved stack

| Layer            | Choice |
| ---------------- | ------ |
| Language         | TypeScript (strict), end-to-end |
| App framework    | Remix (TypeScript backend + React frontend) |
| UI               | React + Tailwind CSS + shadcn/ui (owned, dependency-agnostic) |
| ORM / migrations | Drizzle ORM + drizzle-kit |
| Database         | PostgreSQL (Railway) |
| Hosting          | Railway (server + Postgres + bucket); Docker optional |
| Auth             | Google OAuth 2.0 authorization-code flow (in code) |
| Analytics        | PostHog (analytics + feature flags) |
| Email            | Resend (server-side transactional) |
| Infra assistant  | [Railway MCP](https://docs.railway.com/ai/mcp-server) |

Guiding principles: **KISS**, **YAGNI**, **DRY across projects**, **boring technology**, **type-safety
non-negotiable**, **testable by construction**, **small blast radius**.

## Install

### From GitHub (recommended)

In any Claude Code session, add this repo as a marketplace and install the plugin:

```
/plugin marketplace add elitez-engineering/engineering-skill
/plugin install elitez-engineering@elitez-engineering
```

The repo root contains `.claude-plugin/marketplace.json`, so the repository works as its own
marketplace — no separate marketplace repo is needed.

### From a local checkout

If you've cloned the repo (or are developing the plugin), point the marketplace at the folder:

```
/plugin marketplace add /absolute/path/to/engineering-skill
/plugin install elitez-engineering@elitez-engineering
```

### Verify

Run `/plugin` — `elitez-engineering` should be listed and enabled. Then try `/elitez-review` or
`/elitez-scaffold` to confirm the commands are available.

### Update to the latest version

```
/plugin marketplace update elitez-engineering
/plugin install elitez-engineering@elitez-engineering
```

## Usage

- Starting a project: `/elitez-scaffold my-app`
- Before a PR: `/elitez-review` or ask "review this against Elitez standards" (the reviewer agent
  and skill kick in).
- Building UI: `/elitez-component UserCard "shows avatar, name, and plan badge"`
- Any time you're writing Elitez code, the `elitez-standards` skill loads automatically and steers
  decisions toward the house style.

## Contributing / evolving the standard

The standard is meant to compound. When a project discovers a better pattern:

1. Update the relevant file under `skills/elitez-standards/references/`.
2. Bump the version in `.claude-plugin/plugin.json`.
3. Reinstall/update the plugin in downstream projects.

Keep it concrete and citable — a rule that can't be pointed at in review isn't a standard yet.
