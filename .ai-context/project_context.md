# Project Context — One-Point Employee Portal

One-page orientation. What you would hand a new engineer on day one, before anything else.

> **Reset note (2026-09-03):** this file previously described an unrelated prior project on a
> different technology stack. That content does not apply here and is no longer retained in this
> repository.

## Objective

The **One-Point Employee Portal** is the organisation's centralized self-service gateway through
which employees access HR, payroll, IT, learning, facilities, and other employee services. This
feature workstream — **Employee Internal Transfer Digital Journey** — replaces the current
multi-team, multi-system coordination an employee has to do manually (talking to their manager,
HR validating eligibility, org-info/payroll/IT/facilities updates happening separately) with a
single digital journey inside the portal, giving the employee one place to submit a transfer
request and see its progress.

## Stack

- **Backend:** Laravel/PHP REST API. No source tree exists in this repository yet (no
  `composer.json`, no `artisan`). Versions, packages, and conventions are to be established from
  the repository once code exists, not invented — see
  [`.agent/rules/int-standards.laravel.md`](../.agent/rules/int-standards.laravel.md).
- **Frontend:** Next.js/React. No source tree exists yet either (no `package.json`,
  no `next.config.*`). Routing model, language, and state/data-fetching approach are `[Open]` — see
  [`.agent/rules/int-standards.nextjs.md`](../.agent/rules/int-standards.nextjs.md).
- Backend and frontend are separate concerns: the Laravel API owns business rules, authorization,
  persistence, and state transitions; Next.js consumes the approved API contract and owns
  presentation/client interaction. See the constitution's Architectural Constraints.

## Feature boundary

`emp-internal-transfer` covers:

- Employee-initiated transfer request: proposed department/business unit, location, role/job
  position, effective date, optional reason.
- The approval/validation journey behind it (manager confirmation, HR eligibility validation) to
  the extent the portal needs to orchestrate and expose status for it.
- Status visibility for the employee: current status, and which stakeholder an action is currently
  pending with.
- A downstream orchestration **contract** — org-info update, payroll update, IT
  provisioning/de-provisioning, facilities/new-location arrangement — as things the portal
  triggers/tracks. Whether each is a real integration or a tracked manual step is an open
  discovery question (see [BRD.md](BRD.md)'s Dependencies section) — not assumed either way here.

## Source of truth

This feature's business requirement comes from `source-docs/Requirement for SDD.docx` ("SDD
Developer Assessment — Employee Internal Transfer Digital Journey") — see
[BRD.md](BRD.md) for the full interpretation, and [`source-docs/README.md`](source-docs/README.md)
for the registry entry. Note: this is an assessment-brief document, not a signed-off enterprise
BRD with a named business sponsor — treat items not explicitly stated in it as open questions or
assumptions, never as confirmed decisions (see BRD.md's discovery-rule note).

## How work happens here

This project runs **Specification-Driven Delivery (SDD) v1.0**. Nothing is implemented by
prompting against the codebase directly.

```
BRD → Spec → Gate 1 (Spec Review) → Plan → Architecture check → Tasks
    → Test-first (Red) → Guided implementation (Green) → Gate 2 → Release
```

Start here, in order:

1. [constitution.md](constitution.md) — the non-negotiables. Read before writing any plan.
2. [BRD.md](BRD.md) — where the requirement is first written down, with decisions vs. open items
   separated.
3. `.agent/rules/int-standards.laravel.md` and `.agent/rules/int-standards.nextjs.md` — always-on
   coding rules for each stack half (currently defaults; no repository code exists yet to verify
   against).
4. [status.md](status.md) — what is in flight right now.

Per-feature artefacts live in `specs/`, `plans/`, `tasks/`, `test_cases/`, keyed by slug. Decisions
live in `decisions/ADR-NNNN-*.md`.

## Current state

**In Discovery.** Day 1 (Discovery + Requirement Analysis) is complete for `emp-internal-transfer`.
No `.spec.md`, `.plan.md`, `.tasks.md`, or implementation code exists yet — none is authorized
until an Approved spec and an approved, test-first task breakdown exist. See
[status.md](status.md).

## Prior project cleanup (2026-09-04 through 2026-09-30)

Across several passes, all prior-project content was identified and permanently removed from this
repository: `SRS.md`, `architecture.md`, `source-docs/README.md`, `source-docs/proposal-extract.md`,
`.agent/rules/int-standards.node.md`, and (confirmed via the INT SDD Blueprint's own canonical
`.ai-context/`/`.agent/` structure to not even be a standard SDD artifact) the `inventory/` folder.
`prompt_history.md`'s pre-2026-09-03 entries were also removed on explicit, repeated request — that
history remains available outside the repo, in persistent memory, not in this repository. `.agent/
workflows/` and `.ai-context/templates/` are retained and kept current — both are genuinely
reusable, project-agnostic scaffolding (the Blueprint's own canonical templates and workflow
purpose descriptions), not prior-project content, once cleaned of that project's specific defaults.
`releases/` and `decisions/` are kept as always-empty generic scaffolding.

There is currently no `architecture.md` for the One-Point Employee Portal — one is expected to be
authored once the Day 5 technical plan or actual Laravel/Next.js scaffolding exists.
