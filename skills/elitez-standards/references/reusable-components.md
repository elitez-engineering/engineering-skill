# Reusable Components & Shared Code

Reuse over reinvention. A component or convention built once and reused across projects is cheaper
than a bespoke solution per project. This is the compounding advantage of the standard.

## React / shadcn component rules

- Base UI comes from **shadcn/ui**, copied into `app/components/ui/` and owned by the project. Style
  with Tailwind; keep variants in the component (e.g. `cva`) rather than scattering class strings.
- **Composition over configuration.** Prefer small composable pieces (`<Card>`, `<CardHeader>`) over
  one component with 15 boolean props. If a component has more than ~5 props, ask whether it should
  be split or use `children`.
- **Presentational components are dumb.** They receive data and callbacks via props and render — no
  data fetching, no direct DB/service calls. Fetch in Remix loaders and pass data down.
- **Typed props, no `any`.** Export the prop type. Provide sensible defaults; make required props
  actually required.
- **Accessible by default.** Semantic HTML, labels tied to inputs, keyboard operability, focus
  states, adequate contrast. Accessibility is part of "done", not a follow-up.
- **Controlled where it matters.** Form inputs are controlled or use the platform form; don't mix.
- Keep a component in one file with its types; colocate its test (`Component.test.tsx`).

## When to extract a component

- Extract when the *same UI knowledge* appears a third time, or when a pattern is clearly reusable
  across features/projects (buttons, form fields, empty states, data tables).
- Don't pre-extract for a single use — that's YAGNI. A little duplication beats the wrong abstraction.

## Shared code across projects

- Cross-project reusable code (UI kit, `env` helper, analytics wrapper, auth/session helpers, zod
  schemas) belongs in a shared package, not copy-pasted. Version it and point projects at it.
- A shared module must be: documented (README + example), typed, tested, and stable in its public
  API. Breaking changes get a version bump and a migration note.
- Keep shared code framework-agnostic where possible so it survives a stack change in one project.

## Backend reusability

- Business logic in `services/*` is written as pure, dependency-injected functions so it can be
  reused across routes and lifted into a shared package without dragging Remix in.
- Centralize third-party access behind a thin wrapper (`lib/analytics.ts`, `services/email/*`) so
  swapping or mocking a provider touches one file.

## The reuse checklist (before you write bespoke code)

1. Does an approved shared component/helper already do this? Use it.
2. Is this the third time we've written something like it? Extract it.
3. Will other Elitez projects want this? Put it in the shared package and document it.
4. Otherwise: write the simplest local version and move on (KISS/YAGNI).
