---
description: Generate a reusable React + Tailwind + shadcn component following Elitez component standards (composition, typed props, accessibility, presentational-only).
argument-hint: "<ComponentName> [what it should do]"
---

Create a reusable component following the Elitez standard. First read the `elitez-standards` skill's
`references/reusable-components.md`.

Component: **$1**
Intent: **$2** (ask if unclear).

Requirements:

- Place it under `app/components/` (or `app/components/ui/` if it's a base UI primitive built on
  shadcn/ui). Reuse existing shadcn primitives instead of reinventing them.
- **Presentational only** — receives data and callbacks via props; no data fetching, no DB/service
  calls. Data is fetched in the Remix loader and passed in.
- **Typed, exported props**; required props are required; sensible defaults. No `any`.
- **Composition over configuration** — prefer `children` / sub-components over many boolean props.
  If it needs more than ~5 props, reconsider the shape.
- **Accessible** — semantic HTML, labels tied to inputs, keyboard operable, visible focus, adequate
  contrast. Style with Tailwind; keep variants in the component (e.g. `cva`).
- **Colocate a Vitest test** (`$1.test.tsx`) covering the key rendering/interaction behavior.
- KISS — build the simplest version that meets the intent; don't pre-generalize.

Output the component file, its test, and a one-line usage example. Note if the component is likely
reusable across projects and should later move into the shared package.
