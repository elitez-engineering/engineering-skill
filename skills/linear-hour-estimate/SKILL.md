---
name: linear-hour-estimate
description: Size Elitez Linear tickets from the written spec using Man-hours 1h/2h/3h/4h/8h (estimate matches). Use when estimating, sizing, recalibrating, or setting Linear hours, story points, or Man-hours; when the user runs /linear-hour-estimate or /elitez-estimate; or when creating child issues that still need an hour label.
---

# Linear hour estimate

Size from the **acceptance criteria**, then set Linear **Man-hours** and **estimate**. An hour includes implement, tests, and the AC check.

Allowed child sizes: **1 / 2 / 3 / 4 / 8**. Skip 5, 6, 7. Do not put `16h` / `24h` / `40h` / `80h+` on a child — split the spec. Epics get Type only.

On every child set:

- one **Type** (`Development` / `Bug` / `Improvement`)
- one **Man-hours** (`1h` `2h` `3h` `4h` `8h`)
- **estimate** equal to that number (Linear may store 8h as 7 points; the **Man-hours label is the source of truth**)

Swap hour labels with `removeLabels` / `addLabels` so Type and flags such as `Decision required` stay. Default child is **2h** or **3h**.

## Pick

Answer from the ACs:

1. How many surfaces change?
2. Does another layer have to move with it?
3. What do you walk when it is done?

| Hours | Surfaces | Downstream | Check | Baseline |
|---|---|---|---|---|
| **1** | One control on an existing screen | None | A few clicks | [ELI-571](https://linear.app/elitez-ai/issue/ELI-571/allow-attaching-a-job-description-when-creating-a-job) JD attach (upload already exists) |
| **2** | Several controls, one known page | Consume an existing command or guard | Happy, invalid, reload | [ELI-718](https://linear.app/elitez-ai/issue/ELI-718/f22-make-company-settings-editable-in-the-frontend) F22 settings |
| **3** | Contract or API shape plus one consumer | Frontend and backend, or API plus a journey | Both sides, still bounded | [ELI-752](https://linear.app/elitez-ai/issue/ELI-752/v-11-progress-reads-design-contracts-mount-and-validate) V-11 progress reads |
| **4** | New page, or several records / functions | Several features or a core model | End-to-end | [ELI-580](https://linear.app/elitez-ai/issue/ELI-580/add-guarded-deletion-for-companies-jobs-candidates-and-contacts); [ELI-717](https://linear.app/elitez-ai/issue/ELI-717/be-05-contracts-package-zod-openapi-problemjson) |
| **8** | Cannot name the screens yet, or the cause is unknown | Wide or unclear | Investigation, spike, or a blocked decision | Hard-to-reproduce production bug; unknown technical definition |

A full UI redesign or multi-platform tenancy change is an epic of 2h–4h children. Hesitating between 4 and 8 means split.

Company copy: Linear document [MeridianOS — Ticket hour estimates](https://linear.app/elitez-ai/document/meridianos-ticket-hour-estimates-8e1854a9e23e).

## Apply

1. Read the issue spec (Linear MCP `get_issue`; GitHub body if the ticket is mirrored). Do not size from stale hour labels.
2. Pick 1 / 2 / 3 / 4 / 8 from the table.
3. `save_issue` with `estimate` and hour-label swap. Keep Type and non-hour labels.
4. If the repo maps GitHub ↔ Linear (`docs/LINEAR-GITHUB.md`), update the pair-table hours in the same turn.
5. Leave Activity history unless the user asked to rewrite it.

Linear MCP: discover tools with `search_tool`, then `use_tool`. If Linear is down, stop after the local size table; do not invent IDs.

## Linear public copy

Do not write calendar duration, agent/AI clock time, reconstructed estimates, or “shipped in N days” on Linear issues, comments, documents, or project updates. Hours on the board are this table only.
