---
description: Size Elitez Linear tickets from the spec using Man-hours 1h/2h/3h/4h/8h and matching estimate.
argument-hint: "[issue ids or 'these tickets']"
---

Size Linear tickets using the `linear-hour-estimate` skill. Read that skill first.

Scope: **$1** (Linear identifiers such as ELI-718, a GitHub issue number in a mirrored repo, or the tickets in this conversation if empty).

For each ticket:

1. Read the written acceptance criteria. Ignore existing hour labels until you have a spec-based size.
2. Pick **1 / 2 / 3 / 4 / 8** from the skill table (surfaces, downstream, check).
3. Set Type if missing, one Man-hours label, and estimate equal to the hours. Keep non-hour labels.
4. If this repo has `docs/LINEAR-GITHUB.md`, update the hours column in the same turn.

Do not put calendar duration or agent clock time on Linear. Return a short table: id, chosen hours, one-line reason from the spec.
