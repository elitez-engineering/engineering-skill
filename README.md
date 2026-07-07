# Elitez Engineering — Claude Code Plugin

A shareable [Claude Code](https://docs.claude.com/en/docs/claude-code) plugin that encodes Elitez's
engineering standards so every project starts from the same **scalable, documented, tested**
foundation. Install it once and Claude automatically writes, reviews, and scaffolds code the Elitez
way — the approved SaaS tech stack, clean-code / KISS rules, testability and unit-test conventions,
reusable-component patterns, and explicit do / don't guardrails.

The point is compounding leverage: **each new project is cheaper to start than the last** because we
reuse standards instead of re-deciding them.

- **Repository:** https://github.com/elitez-engineering/engineering-skill
- **Plugin name / marketplace name:** `elitez-engineering`
- **Version:** `0.2.0`

---

## Table of contents

- [Requirements](#requirements)
- [Install](#install)
  - [From GitHub (recommended)](#from-github-recommended)
  - [From a local checkout](#from-a-local-checkout)
  - [Verify the install](#verify-the-install)
  - [Update to the latest version](#update-to-the-latest-version)
  - [Uninstall](#uninstall)
  - [Troubleshooting](#troubleshooting)
- [What's inside](#whats-inside)
- [The `elitez-standards` skill](#the-elitez-standards-skill)
- [Commands](#commands)
- [The `elitez-reviewer` agent](#the-elitez-reviewer-agent)
- [Railway MCP server (bundled)](#railway-mcp-server-bundled)
- [The approved stack](#the-approved-stack)
- [Core principles](#core-principles)
- [Standards at a glance](#standards-at-a-glance)
- [Definition of done](#definition-of-done-engineering)
- [Everyday usage & recipes](#everyday-usage--recipes)
- [Repository layout](#repository-layout)
- [Contributing / evolving the standard](#contributing--evolving-the-standard)
- [FAQ](#faq)

---

## Requirements

- **Claude Code** (CLI, desktop, web, or an IDE extension) with plugin support — run `/plugin` to
  confirm the plugin manager is available.
- **Git** on your machine (Claude Code fetches marketplaces over git).
- Read access to `github.com/elitez-engineering/engineering-skill` (public, or your GitHub auth for a
  private mirror).

No language runtime is required to *install* the plugin — it ships prompts, skills, commands, and an
agent, not compiled code. The **projects** you build with it use the [approved stack](#the-approved-stack).

---

## Install

This repository is **its own marketplace**: the repo root contains `.claude-plugin/marketplace.json`,
so you add the repo as a marketplace and then install the single plugin it exposes. No separate
marketplace repo is needed.

### From GitHub (recommended)

In any Claude Code session, run these two slash commands:

```
/plugin marketplace add elitez-engineering/engineering-skill
/plugin install elitez-engineering@elitez-engineering
```

- The first command registers this repo as a marketplace named `elitez-engineering`.
- The second installs the `elitez-engineering` plugin *from* that marketplace. The
  `plugin@marketplace` syntax is `<plugin-name>@<marketplace-name>` — both happen to be
  `elitez-engineering` here.

Prefer the full clone URL? These are equivalent:

```
/plugin marketplace add https://github.com/elitez-engineering/engineering-skill
/plugin marketplace add git@github.com:elitez-engineering/engineering-skill.git
```

### From a local checkout

Useful when you're developing the plugin or working offline. Clone the repo, then point the
marketplace at the folder (the folder is named `engineering-skill` after a default clone):

```
git clone https://github.com/elitez-engineering/engineering-skill
```

```
/plugin marketplace add /absolute/path/to/engineering-skill
/plugin install elitez-engineering@elitez-engineering
```

### Verify the install

```
/plugin
```

`elitez-engineering` should appear in the list and be **enabled**. Then confirm the pieces loaded:

- Type `/elitez-` — you should see `/elitez-scaffold`, `/elitez-review`, and `/elitez-component`.
- Ask Claude to "review this against Elitez standards" in a code project — the `elitez-standards`
  skill and `elitez-reviewer` agent should engage.

### Update to the latest version

When the standard evolves (a new pattern is added, the version is bumped), refresh your local copy:

```
/plugin marketplace update elitez-engineering
/plugin install elitez-engineering@elitez-engineering
```

`marketplace update` pulls the latest commit from GitHub; re-running `install` upgrades the installed
plugin to the new version.

### Uninstall

```
/plugin uninstall elitez-engineering@elitez-engineering
```

To also forget the marketplace: `/plugin marketplace remove elitez-engineering`.

### Troubleshooting

| Symptom | Likely cause | Fix |
| ------- | ------------ | --- |
| `marketplace add` fails | Git can't reach the repo (auth / network) | Confirm you can `git clone` the URL manually; for a private mirror, set up GitHub auth first. |
| Plugin not listed after install | Marketplace name vs plugin name confusion | Both are `elitez-engineering`; install with `elitez-engineering@elitez-engineering`. |
| `/elitez-*` commands missing | Plugin installed but disabled | Run `/plugin`, enable `elitez-engineering`, restart the session. |
| Skill never auto-loads | Not working in a code context | The skill loads when you write/review/scaffold code; you can also invoke it explicitly. |
| Stale content after an upstream change | Local marketplace cache | Run `/plugin marketplace update elitez-engineering` then reinstall. |

---

## What's inside

```
elitez-engineering/
├─ .claude-plugin/
│  ├─ plugin.json           # plugin manifest (name, version, description, keywords)
│  └─ marketplace.json      # marketplace manifest — repo installs as its own marketplace
├─ .mcp.json                # bundled MCP servers (Railway) — auto-loaded when the plugin is enabled
├─ skills/
│  └─ elitez-standards/
│     ├─ SKILL.md           # the source-of-truth skill (auto-loads on Elitez code work)
│     └─ references/
│        ├─ tech-stack.md            # approved stack, versions, project layout, setup
│        ├─ clean-code.md            # KISS/DRY/YAGNI, naming, functions, TS strictness
│        ├─ testing.md               # test pyramid, Vitest, testability, CI gates
│        ├─ reusable-components.md   # React/shadcn + shared-package patterns
│        └─ do-and-dont.md           # fast pre-PR do/don't checklist
├─ commands/
│  ├─ elitez-scaffold.md    # /elitez-scaffold  — new project on the approved stack
│  ├─ elitez-review.md      # /elitez-review    — standards code review with a verdict
│  └─ elitez-component.md   # /elitez-component — reusable React/shadcn component + test
├─ agents/
│  └─ elitez-reviewer.md    # subagent that reviews code against the standards
└─ README.md
```

Five moving parts work together:

1. **The skill** is the knowledge base. It auto-loads and steers Claude toward the house style.
2. **The commands** are explicit entry points for the three most common tasks (scaffold, review,
   build a component).
3. **The agent** is a dedicated reviewer you can delegate to.
4. **The bundled MCP server** (Railway) lets Claude manage your Railway infrastructure directly.
5. **The manifests** make it installable as a one-line marketplace.

---

## The `elitez-standards` skill

The single source of truth for how Elitez builds web SaaS. It **auto-loads** whenever you write,
review, scaffold, or refactor code in an Elitez project, so you don't have to remember to invoke it.

**When it applies**

- Starting a new Elitez project or feature.
- Writing or reviewing backend (Remix loaders/actions, services), frontend (React components), or
  database (Drizzle schema/migrations) code.
- Choosing between two approaches and wanting the house style / approved dependency.
- Doing a code review and needing a concrete, citable standard.

**How it's organized** — a short `SKILL.md` (stack summary, core principles, definition of done)
plus five focused reference files. The skill's rule is: *when a decision is covered by a reference
file, read that file first and follow it; when it isn't, fall back to the core principles and leave
the codebase more consistent than you found it.*

| Reference file | Covers |
| -------------- | ------ |
| `references/tech-stack.md` | Approved stack, versions, project layout, and setup for Railway + Drizzle + Google OAuth + PostHog + Resend. |
| `references/clean-code.md` | KISS / DRY / YAGNI in practice, naming, function & module size, error handling at boundaries, comments policy, strict-TypeScript rules. |
| `references/testing.md` | The test pyramid, unit vs integration vs E2E, Vitest conventions, arranging code for testability, CI gates. |
| `references/reusable-components.md` | Building shareable React/shadcn components and shared TS packages: props contracts, accessibility, composition over configuration. |
| `references/do-and-dont.md` | A fast checklist of concrete do's and don'ts to scan before opening or approving a PR. |

---

## Commands

Three slash commands cover the highest-frequency tasks. Each one reads the `elitez-standards` skill
first so its output cites concrete rules.

### `/elitez-scaffold [project-name]`

Scaffolds a new Elitez SaaS project on the approved stack — a minimal, working, standards-compliant
starting point (KISS, no speculative features). It sets up:

- Remix + Vite + TypeScript (`strict: true`)
- Tailwind + shadcn/ui with an owned `app/components/ui/` (seeded with a `Button`)
- Drizzle + PostgreSQL: `drizzle.config.ts`, `app/db/schema.ts` (example `users` table),
  `app/db/index.ts`, and an initial committed migration
- zod-validated env (`app/lib/env.server.ts`) that fails fast, plus `.env.example`
- A stubbed Google OAuth authorization-code flow in `app/services/auth/google.ts` (flagged for
  Security review)
- PostHog wrapper (`app/lib/analytics.ts`) and server-side Resend helper (`app/services/email/`)
- ESLint + Prettier + Vitest with an example service test and `test` / `lint` / `typecheck` scripts
- A README documenting the stack, env vars, and how to run / test / migrate

It then runs typecheck, lint, and the test suite and reports results. **Example:**

```
/elitez-scaffold billing-portal
```

### `/elitez-review [path-or-diff-scope]`

Reviews the current changes (defaults to the git diff vs the base branch) against every axis of the
standard: stack, strict types, structure (thin loaders/actions, logic in `services/*`), simplicity,
validation & errors, database migrations, security, testing, reuse, and hygiene.

Output is grouped as **Blocking / Should-fix / Nits**, each finding gives `file:line`, the violated
standard (citing the reference file), and the concrete fix — ending with a verdict of **approve**,
**approve-with-nits**, or **changes-requested**. It does not rubber-stamp. **Examples:**

```
/elitez-review
/elitez-review app/services/billing
```

### `/elitez-component <ComponentName> [what it should do]`

Generates a reusable React + Tailwind + shadcn component that follows the component standard:
presentational-only (no data fetching), typed and exported props, composition over configuration
(reconsider beyond ~5 props), accessible by default, and shipped with a colocated Vitest test. It
also flags whether the component likely belongs in the shared cross-project package. **Example:**

```
/elitez-component UserCard "shows avatar, name, and plan badge"
```

---

## The `elitez-reviewer` agent

A dedicated subagent (tools: `Read`, `Grep`, `Glob`, `Bash`) that reviews code against the standards.
Use it **proactively** after implementing a feature or before a merge, or any time you ask for a
standards / code review of Elitez code.

It reads the skill first so every finding cites a concrete rule, checks the same axes as
`/elitez-review`, and can run `tsc --noEmit`, the linter, and the test suite to back its findings
with evidence. It **reviews but does not rewrite** the code, groups findings as
**Blocking / Should-fix / Nits**, and ends with a clear verdict. Two important guardrails:

- **It escalates** anything touching auth, crypto, PII, or session handling to a human Security
  review — it will not approve those itself.
- **It won't loop forever** — if two passes don't resolve a point, it states its recommendation and
  hands back.

---

## Railway MCP server (bundled)

Railway is the approved hosting platform, so the plugin bundles the **Railway MCP server**. When the
plugin is enabled, the server starts automatically and its tools appear in Claude's toolkit — letting
Claude inspect and manage Railway services (projects, deployments, variables, logs) in natural
language instead of you hand-copying values from the dashboard.

**Config** (shipped as `.mcp.json` at the plugin root, auto-discovered when the plugin is enabled):

```json
{
  "mcpServers": {
    "railway": {
      "command": "railway",
      "args": ["mcp"]
    }
  }
}
```

The current Railway MCP is served by the **Railway CLI** (`railway mcp`), which translates requests
into CLI workflows.

**Prerequisites**

1. **Install the Railway CLI** — see https://docs.railway.com/guides/cli, e.g.
   `bash <(curl -fsSL https://railway.com/install.sh)` (or `brew install railway`, `npm i -g @railway/cli`).
2. **Authenticate** once: `railway login`.
3. The MCP server is a local **stdio** server; it uses your authenticated CLI session — no API token
   is committed anywhere.

**Approval & lifecycle**

- Like any bundled MCP server, Railway goes through the **same per-server approval** as a project
  `.mcp.json` — Claude Code prompts you to trust it the first time the plugin is enabled.
- After enabling (or after a plugin update), run `/reload-plugins` or restart Claude Code so the MCP
  server picks up the current version.
- Prefer the hosted option? Railway also offers a remote HTTP MCP at `mcp.railway.com` (set up via
  `railway setup agent --remote`); swap the local config for that if you don't want the CLI installed.
- If `railway` isn't on your PATH, the server won't start — install/authenticate the CLI (above),
  then `/reload-plugins`.

> Security: Railway management actions (deploys, variable changes) can affect production. Treat the
> MCP as authorized-use only, and route infra changes through the same review discipline as code.

---

## The approved stack

This is the default for every Elitez SaaS project. Deviating requires a written justification in the
project README (what, why, and the tradeoff). "Boring technology" is the rule; novelty is the
exception that must be argued for.

| Layer            | Choice | Why |
| ---------------- | ------ | --- |
| Language         | TypeScript (`strict`), end-to-end | One language BE + FE, fast AI-assisted dev, strong types |
| App framework    | Remix (TypeScript backend + React frontend) | Unified loaders/actions, progressive enhancement, web standards |
| UI               | React + Tailwind CSS + shadcn/ui (owned, copied in) | Dependency-agnostic components you own and can edit |
| ORM / migrations | Drizzle ORM + drizzle-kit | Type-safe SQL, explicit versioned migrations |
| Database         | PostgreSQL (Railway) | Boring, proven, relational integrity |
| Hosting          | Railway (server + Postgres + bucket); Docker optional | One platform, simple deploys |
| Auth             | Google OAuth 2.0 authorization-code flow (in code) | No heavy auth-vendor lock-in; owned in-repo |
| Analytics        | PostHog | Product analytics + feature flags |
| Email            | Resend (server-side transactional) | Simple transactional email API |
| Infra assistant  | [Railway MCP](https://docs.railway.com/ai/mcp-server) | Manage Railway from the agent when authorized |

**Reference project layout** (from `references/tech-stack.md`):

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

---

## Core principles

1. **KISS** — the simplest design that satisfies the requirement wins. No speculative abstraction.
2. **YAGNI** — don't build for hypothetical futures. Three similar lines beat a premature helper.
3. **DRY across projects, not just within one** — prefer a shared component/convention over a
   bespoke per-project solution.
4. **Boring technology** — prefer proven, documented tech; novelty needs a written justification.
5. **Type-safety is non-negotiable** — `strict` TypeScript, no `any` in committed code; types flow
   from the Drizzle schema and zod inputs to the UI.
6. **Testable by construction** — pure logic separated from I/O so it can be unit-tested without
   mocking everything.
7. **Small blast radius** — a failure in one module/route should not cascade. Validate at boundaries.
8. **Total cost of ownership** — optimize for maintenance, onboarding, and readability, not just
   time-to-first-demo.

---

## Standards at a glance

Deep detail lives in the reference files; here's the shape of each so you know what the plugin
enforces.

### Clean code (`clean-code.md`)

- **KISS**: simplest thing that works; plain function over class, class over framework, explicit over
  clever. If clever code needs a comment to explain *how*, rewrite it to be obvious.
- **YAGNI**: no config flags / extension points / "just in case" params no caller uses; wait for the
  third occurrence before abstracting; delete dead code.
- **DRY the right kind**: extract when the *same knowledge* is duplicated, not when two blocks merely
  look similar.
- **Naming**: intent not mechanism (`activeSubscribers`, not `filteredList`); booleans read as
  predicates (`isEnabled`, `hasAccess`).
- **Functions/modules**: one thing at one level of abstraction; ~≤40 lines and ≤3 args as smell
  thresholds; business logic in `services/*`, not loaders/actions or components.
- **TypeScript**: `strict: true`, no `any` (use `unknown` + narrowing), no `!` to silence the
  compiler; types from Drizzle + zod; discriminated unions + exhaustive `switch` over boolean flags.
- **Errors**: validate at boundaries only; never swallow (`catch {}`); log with context or rethrow.
- **Comments**: default to none; write one only when the *why* is non-obvious.

### Testing (`testing.md`)

- **Pyramid**: many unit tests (pure logic in `services/*`, `lib/*`), some integration tests
  (loaders/actions + DB against a real migrated test Postgres), few E2E (login, primary conversion).
- **Vitest** for unit + integration; Playwright for E2E. **Do not mock the database** for logic that
  depends on real query behavior — mocked DBs pass while real migrations break.
- **What to test**: every non-trivial `services/` function (happy / boundary / error); behavior and
  public contracts, not private details; bug fixes ship a regression test.
- **Testability**: separate pure logic from I/O; inject dependencies (db, email, clock) rather than
  importing singletons; don't reach for `Date.now()` / `Math.random()` inside logic.
- **CI gates (must pass to merge)**: `tsc --noEmit` (zero errors), ESLint (zero errors),
  `vitest run` green with an 80%+ coverage floor on `services/`, and a Drizzle migration-sync check.

### Reusable components (`reusable-components.md`)

- Base UI from shadcn/ui, copied into `app/components/ui/` and **owned**; variants via `cva`.
- **Composition over configuration** — small composable pieces over a 15-prop mega-component;
  reconsider past ~5 props.
- **Presentational components are dumb** — data and callbacks via props; fetch in loaders.
- Typed, exported props; accessible by default (semantic HTML, labels, keyboard, focus, contrast).
- Extract on the **third** occurrence; cross-project reusable code lives in a versioned, documented,
  tested shared package — not copy-paste.

### Do & Don't (`do-and-dont.md`)

A fast pre-PR checklist across Stack, Types, Structure, Simplicity, Validation & errors, Database &
migrations, Security & secrets, Testing, Reuse, and Hygiene. A few of the sharpest lines:

- ❌ Don't add a dependency when the stack already covers the need.
- ❌ No `any` in committed code; no `!` to silence the compiler.
- ❌ Don't call the DB or services directly from React components.
- ❌ Never swallow errors (`catch {}`); no silent failures.
- ❌ Never commit secrets; never send PII in PostHog events; email is server-side only.
- ❌ Don't mock the database for logic that depends on real query behavior.
- ❌ Don't bypass CI gates, signing, or review to ship faster.

---

## Definition of done (engineering)

A change is done when:

- it compiles under strict TS with no `any`;
- lint passes;
- unit tests cover the new logic and the suite is green;
- it follows the approved stack and the relevant reference file;
- inputs are validated at boundaries;
- no secret is committed; and
- if it introduces a pattern other projects will want, it is extracted or documented so the next
  project reuses it.

---

## Everyday usage & recipes

- **Start a project:** `/elitez-scaffold my-app`
- **Before a PR:** `/elitez-review` — or just ask *"review this against Elitez standards"* and the
  reviewer agent + skill kick in.
- **Build UI:** `/elitez-component UserCard "shows avatar, name, and plan badge"`
- **Ambient guidance:** any time you're writing Elitez code, the `elitez-standards` skill loads
  automatically and steers decisions toward the house style — no command needed.

A typical feature loop:

1. `/elitez-scaffold` (once per project) or work in an existing standards-compliant repo.
2. Implement the feature; the skill nudges you toward thin loaders/actions and logic in `services/*`.
3. `/elitez-component` for any new UI primitive.
4. `/elitez-review` (or hand off to `elitez-reviewer`) before opening the PR.
5. Fix Blocking / Should-fix findings; open the PR with green CI gates.

---

## Repository layout

See [What's inside](#whats-inside) for the annotated tree. Key entry points:

- `.claude-plugin/marketplace.json` — makes the repo installable as its own marketplace (`source: ./`).
- `.claude-plugin/plugin.json` — the plugin manifest (name, version, keywords).
- `skills/elitez-standards/SKILL.md` — start here to understand or extend the standard.

---

## Contributing / evolving the standard

The standard is meant to **compound**. When a project discovers a better pattern:

1. Update the relevant file under `skills/elitez-standards/references/` — keep it concrete and
   citable (a rule you can't point at in review isn't a standard yet).
2. Bump the version in `.claude-plugin/plugin.json` (and `marketplace.json` metadata).
3. Commit and push to `main`.
4. Downstream projects update with:
   ```
   /plugin marketplace update elitez-engineering
   /plugin install elitez-engineering@elitez-engineering
   ```

Keep changes small and reviewable, and prefer adding a citable rule over prose. If a new command or
agent is genuinely reusable across projects, add it here rather than re-inventing it per repo.

---

## FAQ

**Why is the plugin name the same as the marketplace name?**
This repo is a single-plugin marketplace. `marketplace.json` names the marketplace `elitez-engineering`
and lists one plugin, also `elitez-engineering` (`source: ./`). So you install with
`elitez-engineering@elitez-engineering` (`<plugin>@<marketplace>`).

**Do I need to install anything else to build a project?**
No extra Claude Code setup — but the projects you build use the [approved stack](#the-approved-stack)
(Node/TypeScript, Remix, Postgres via Railway, etc.). `/elitez-scaffold` sets those up for you.

**Can I use just the skill without the commands?**
Yes. The skill auto-loads on code work regardless of whether you use the commands. The commands and
agent are convenience entry points on top of the same standard.

**How do I propose a change to the standard?**
Edit the matching reference file, bump the version, and push (see
[Contributing](#contributing--evolving-the-standard)). Downstream projects pick it up on the next
`marketplace update` + reinstall.
