# Project Constitution — One-Point Employee Portal

**Owner:** TBD (Technical Lead not yet assigned) · **Adopted:** 2026-09-03 · **Version:** v1.0 (Provisional)

Governs every feature this repository will build. Written once, amended rarely, and amended only
through the same review rigour as a spec. Each spec operates inside this document and never
restates it.

Standard in force: **INT Engineering Guidelines — Specification-Driven Delivery (SDD) v1.0**,
per `source-docs/INT SDD BluePrint - V1.0.pdf` (see [SRS.md](SRS.md) / registry note below). Rules
below may be stricter than the standard; none may weaken it.

> **Reset note (2026-09-03):** this repository previously held constitution content for an
> unrelated project ("Empty Floor + Circle Tap", a real-estate marketplace on Express/TypeScript).
> That content is archived at
> [`_archive/empty-floor-legacy/constitution.md`](_archive/empty-floor-legacy/constitution.md) and
> does not apply here. This document is a fresh v1.0 for the One-Point Employee Portal.

> **Provisional values.** Lines marked `[Provisional]` are carried over as sensible SDD defaults or
> derived from the assessment brief, not from a signed-off organisational decision. They are
> binding until amended. Lines marked `[Open]` are not yet decided and block any spec that depends
> on them.

---

## Governance & Roles

| Role | Person | Responsibility |
|---|---|---|
| Technical Lead / Architect / constitution owner | **TBD** | Not yet assigned for this project |
| Spec Author | **Aditya Hazra** | Authors specs/plans/tasks for this feature (confirmed 2026-09-08, `emp-internal-transfer`) |
| Gate 1 Reviewer | **Sourav Kumar Maity** | Independent peer reviewer — confirmed distinct from the author (confirmed 2026-09-08, `emp-internal-transfer`) |

Technical Lead remains `[Open]` — no roster entry yet. Spec Author and Gate 1 Reviewer are
confirmed per-feature as of `emp-internal-transfer`; if a later feature uses different people,
update this table rather than assuming these two carry forward automatically.

## Testing Discipline

- Test-first is mandatory for every API endpoint and every state-changing operation. No exceptions
  for "simple" endpoints. Tests exist, are reviewed, and are confirmed **failing** before
  implementation.
- Framework: whichever PHPUnit/Pest (backend) and frontend test framework are established when the
  project is first scaffolded — see `.agent/rules/int-standards.laravel.md` and
  `.agent/rules/int-standards.nextjs.md`. `[Open]` until scaffolding exists.
- **Coverage floor: 80% line coverage on changed files, measured on changed files, not repo-wide**
  `[Provisional]` — carried over as INT's standard SDD floor; not yet confirmed against a signed
  organisational policy for this project. Coverage is a floor, not a target to write to.
- A test that passes before its implementation exists is testing nothing. The Red confirmation is
  recorded on the task and verified at Gate 2.

## Security Posture

- **No PII in logs at any level, including debug.** PII here means at minimum: full name, mobile
  number, email, government ID, salary/payroll data, and full postal address. This applies to
  backend application logs and frontend console/analytics output alike.
- Authentication and authorization are required on **every** protected API route and every
  frontend screen that shows employee-specific data. "Public because X" is a recorded decision;
  silence is not.
- Every new or changed endpoint ships with an explicit rate-limit decision. "None, because X" is a
  valid decision; silence is not.
- Secrets are supplied only through the repository's environment/secret-management mechanism once
  established (e.g. Laravel's `.env` + config layer). Secrets never enter a spec, plan, task,
  prompt, or `prompt_history.md`.
- Auth mechanism (Sanctum/Passport/session guards for Laravel; how the Next.js frontend holds and
  sends credentials) is **`[Open]`** — a Day 5 technical-plan decision, not to be assumed here.

## Architectural Constraints

- **Backend/frontend boundary is explicit and non-negotiable:** the Laravel API owns business
  rules, authorization, persistence, and state transitions for the transfer journey. The Next.js
  app owns presentation and client interaction against the API's approved contract. The frontend
  is never the source of truth for a business rule or an authorization decision.
- **No new datastore, queue, or external service** without an ADR recorded in `decisions/`.
- Use the versions, packages, and conventions already present in the repository once code exists;
  do not invent dependency versions during discovery, and do not introduce a second library for a
  role the repository already fills.
- Stack: Backend — Laravel/PHP REST API (version `[Open]` — no scaffold yet). Frontend —
  Next.js/React (version, router, and language `[Open]` — no scaffold yet).

## Non-Functional Baselines

`[Provisional]` — no production measurement exists yet; these stay binding as design targets and
are refined once real traffic/monitoring produces numbers, or once an approved organisational
baseline supersedes them.

- **p95 API latency target: < 400 ms.**
- **Availability target: 99.9%.**
- Every spec that introduces or changes an endpoint states which of the above it is expected to
  meet, or explicitly argues why it's an exception (e.g. a heavy report/export endpoint).

## Versioning Rules

- API versioning scheme (e.g. path-versioned `/api/v1`) — `[Open]`, to be set once the Laravel
  project is scaffolded and recorded here.
- A breaking change to a released endpoint requires a version bump, an ADR documenting the break,
  and coordination with the Next.js frontend. Silent breaking changes are prohibited.
- The API contract artefact (OpenAPI or equivalent, format TBD) is updated in the same task as any
  endpoint, payload, or response change, once established.

---

## Repository & Branching

- One feature branch per spec, traceable to the spec file of the same slug (exact naming
  convention `[Open]` pending repository/VCS setup — this workspace is not currently a git
  repository).
- `.agent/` and `.ai-context/` are internal-repo only and are not shipped to any client-facing
  release artefact.
- Trivial-change tier — config bumps, dependency version updates, formatting, and docs-only edits
  with no behaviour change — skips spec/plan/tasks but still requires a `status.md` note and a
  reviewed change. Anything that changes behaviour is not trivial.

## SDD Chain (INT amendments, carried forward)

1. **Granular specification is mandatory.** The chain is **BRD → Spec → Gate 1 → Plan → Tasks →
   Test-first → Implementation → Gate 2 → Release**, per the assessment brief.
2. **Spec Review (Gate 1) is the first quality gate.** Every spec is reviewed and approved before
   development begins — a real review, not a presentation.
3. **Slugs and sub-identifiers are mandatory** for every spec, plan, and task, so prompts reference
   identity (`<slug>.T03`) rather than re-describing the feature.
4. **Status is maintained for every item** — BRD entries, specs, plans, and tasks — with progress
   updates in `status.md` the same day work happens.
5. No implementation code, `.spec.md`, `.plan.md`, or `.tasks.md` is generated ahead of an
   Approved spec and an approved, test-first task breakdown.
