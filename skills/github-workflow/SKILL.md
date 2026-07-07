---
name: github-workflow
description: The mandatory GitHub workflow for EVERY coding agent (ProductEngineer, DevOps, QA, SecurityEngineer, and any future coder). Use before you touch a git repo — starting a change, branching, committing, or opening a pull request. Encodes the three non-negotiables (always open a PR, always a proper PR description, always branch from the latest main) plus branch naming, commit conventions, a PR-description template, review/merge expectations, and a pre-PR self-check.
---

# GitHub Workflow — Rules for All Coding Agents

This skill is the single source of truth for how every coding agent works in GitHub across all
Elitez projects. It applies to **anyone who writes code** — ProductEngineer, DevOps, QA,
SecurityEngineer, and any future coding agent. Follow it every time you change a repository.

Read this before your first git command on a task. When a step here conflicts with a habit or an
old template, this skill wins.

## The three non-negotiables

1. **ALWAYS open a Pull Request. NEVER push directly to `main`.**
   Every change — even a one-line fix — lands through a PR. Direct pushes to `main` (or any
   protected/default branch) are prohibited. If you find yourself on `main` with changes, move them
   to a branch before pushing.

2. **ALWAYS write a PROPER PR description.**
   A PR without a real description is not ready. It must state *what* changed, *why*, how it was
   tested, and link the issue it closes. Use the template below.

3. **ALWAYS branch from the LATEST `main`.**
   Before creating a branch, fetch and fast-forward `main` so you start from the current tip. Never
   branch from a stale local `main` or from another feature branch unless the task explicitly builds
   on it.

If you can only remember three things, remember these.

## Standard procedure (start to merge)

```sh
# 1. Start from the latest main — always fetch + pull first.
git checkout main
git fetch origin
git pull --ff-only origin main

# 2. Create a well-named branch off that fresh main (see "Branch naming").
git checkout -b <type>/<short-slug>

# 3. Make your change. Keep the diff focused on the task; no drive-by refactors.

# 4. Stage intentionally — name the files, don't blind `git add -A`.
git add path/to/changed/file

# 5. Commit following "Commit conventions" (author + no co-author trailer).
git commit

# 6. Push the branch and open a PR (never push to main).
git push -u origin <type>/<short-slug>
# then open the PR with a proper description (see template)

# 7. Address review, keep the branch up to date with main, merge when approved.
```

### Keeping a branch current

If `main` moves while your PR is open, update your branch before merge:

```sh
git fetch origin
git rebase origin/main      # preferred: linear history; resolve conflicts, don't discard work
git push --force-with-lease  # only your own feature branch, never a shared/base branch
```

Prefer resolving conflicts over discarding either side. Never force-push `main` or a shared base
branch.

## Branch naming

Pattern: `<type>/<short-kebab-slug>`. Keep it lowercase, hyphenated, and specific.

| Type       | Use for                                             | Example                          |
| ---------- | --------------------------------------------------- | -------------------------------- |
| `feat/`    | A new feature or capability                         | `feat/google-oauth-login`        |
| `fix/`     | A bug fix                                           | `fix/cart-total-off-by-one`      |
| `chore/`   | Tooling, deps, config, non-user-facing maintenance  | `chore/bump-drizzle-0-30`        |
| `refactor/`| Behavior-preserving restructuring                   | `refactor/extract-auth-service`  |
| `docs/`    | Docs / standards only                               | `docs/api-error-codes`           |
| `test/`    | Adding or fixing tests only                         | `test/checkout-integration`      |

Optionally suffix the ticket id for traceability: `fix/cart-total-off-by-one-CHRA-142`.

## Commit conventions

- **Subject line:** imperative mood, ≤ ~72 chars, no trailing period.
  `Add Google OAuth authorization-code login`
- **Body (when the change isn't self-evident):** explain the *why* and any tradeoff, wrapped at
  ~72 cols. Reference the ticket id.
- **Author name:** set the commit author to the company-standard identity before committing:

  ```sh
  git config user.name "raflylesmana3003"
  ```

  A repo checkout may carry a local override, so set it explicitly per repo. Do **not** change the
  author *email*.

- **Co-author trailer — DO NOT ADD ONE.** The commit message must contain **zero**
  `Co-Authored-By:` trailers of any kind (not Claude, not Paperclip, not your agent name). This
  follows the board `commit-hygiene` directive, which is the authoritative source of truth for
  commit trailers. If any template or habit would add such a line, remove it before committing.

- **Do not** rewrite existing published history, and **never** use `--no-verify` or skip commit
  signing to work around a failing hook — fix the underlying issue.

Verify before you push:

```sh
git log -1 --format='%b' | grep -i 'co-authored-by' && echo "FAIL: co-author trailer present" || echo "OK: no co-author trailer"
```

The check must print `OK: no co-author trailer`.

> Note on convention drift: some older task descriptions still ask for a
> `Co-Authored-By: Paperclip <noreply@paperclip.ing>` trailer. That instruction is **superseded** by
> the board `commit-hygiene` directive — do not add it. When in doubt, no co-author trailer.

## PR description template

Every PR body uses this structure. Fill in every section; delete none.

```md
## Summary
<1–3 sentences: what changed and why. The "why" matters more than the "what".>

## Changes
- <bullet the meaningful changes a reviewer should look at>

## Test plan
- [ ] <how you verified this — commands run, tests added/passing, manual checks>
- [ ] <edge cases considered>

## Linked issue
Closes <TICKET-ID>   <!-- e.g. Closes CHRA-142 — use the real ticket so it auto-links/auto-closes -->
```

Rules for a *proper* description:

- The **Summary** is not the branch name or the commit subject — it explains intent to a reviewer
  who wasn't in your head.
- The **Test plan** must be real. "N/A" is only acceptable for pure-docs changes, and say why.
- Always link the issue with a closing keyword (`Closes`/`Fixes`) so merge auto-resolves it.
- Keep the PR scoped to one logical change. If the diff sprawls, split it.

## Review & merge expectations

- **At least one approving review** before merge. Security-sensitive changes (auth, crypto, secrets,
  permissions, PII, the agent tool surface) require Security Engineer sign-off — loop them in on the
  PR, not after merge.
- **CI must be green.** Do not merge red, and do not bypass required checks, signing, or review to
  ship faster.
- **Respond to every review comment** — resolve it in code or reply with the reasoning. Don't
  silently dismiss threads.
- **Keep the branch current with `main`** (rebase/merge) before merging so you're integrating what
  actually ships.
- **Prefer squash merge** for a clean, linear `main` history unless the project standard says
  otherwise. Delete the branch after merge.
- The **author does not approve their own PR.** Reviewer owns the approval; author owns addressing
  feedback.

## Pre-PR self-check

Run through this before you open (or ask for review on) a PR. If any answer is "no", fix it first.

- [ ] Branch was created from the **latest** `main` (fetched + pulled), not a stale base.
- [ ] I am **not** pushing to `main` — the change is on a properly named branch.
- [ ] The diff is **scoped** to this task — no unrelated refactors or stray files.
- [ ] Commit subjects are imperative and clear; body explains non-obvious *why*.
- [ ] Commit author is `raflylesmana3003` and there is **no** `Co-Authored-By:` trailer
      (`git log -1 --format='%b' | grep -i 'co-authored-by'` prints nothing).
- [ ] No secrets, credentials, tokens, or `.env` files are staged.
- [ ] Tests/build relevant to the change pass locally (or I've said why not).
- [ ] PR description is filled from the template: Summary, Changes, Test plan, and a linked issue
      with a closing keyword.
- [ ] Security-sensitive change → Security Engineer is tagged.

## Related standards

- `commit-hygiene` (board directive) — the authoritative rule on commit trailers; this skill defers
  to it for the no-co-author rule.
- `elitez-standards` — the house style for code you're changing (stack, clean-code, testing,
  reusable components). Its `references/do-and-dont.md` is a good final scan before you open a PR.
