# Project Status Board

_Last updated: 2026-09-30 — updated by: Aditya Hazra (Developer)_

Single source of truth for **what is happening right now**. Updated the same day by whoever last
touched an artefact.

> **Reset note (2026-09-03):** this board previously tracked an unrelated prior project (a
> multi-spec programme on a different technology stack). That content — plus the rest of the
> leftover prior-project artefacts (`SRS.md`, `architecture.md`, `source-docs/README.md`,
> `source-docs/proposal-extract.md`, `.agent/rules/int-standards.node.md`) — was confirmed not
> needed and permanently removed from the repo; the context is preserved outside the repo instead.
> This board tracks `emp-internal-transfer` only.

---

## Programme status

| Item | State |
|---|---|
| Feature | `emp-internal-transfer` — Employee Internal Transfer Digital Journey |
| Stage | **Draft v1.1 — In Peer Review (Gate 1 re-review)** |
| Owner | Developer (Aditya Hazra) |
| Gate 1 Reviewer | **Sourav Kumar Maity** |
| SDD chain position | Day 4 (Gate 1 review + revision) complete; awaiting re-review before Day 5 (Technical Plan) |

## Active specs

| Spec ID | Title | Status | Owner | Last Updated | Notes |
|---|---|---|---|---|---|
| `emp-internal-transfer` | Employee Internal Transfer Digital Journey | **Draft v1.1 — In Peer Review (Gate 1 re-review)** | Developer | 2026-09-30 | Revised in response to both Gate 1 review passes: `BRD.md` gained Q12/Q13 and controlled assumptions for Q07/Q08; spec gained `AC18`/`API04` (downstream-completion mechanism), a fixed `hr_approved`→`completed` branch, a corrected `AC12` (no more non-completed restriction), and a "Revision Notes" section dispositioning every review item. **Next:** resubmit to Sourav Kumar Maity for Gate 1 re-review |

## Baseline artefacts

| Artefact | Status | Note |
|---|---|---|
| `constitution.md` | Reset — v1.0 (Provisional) | Fresh for this project; several values `[Open]`/`[Provisional]` pending confirmation |
| `project_context.md` | Reset — Current | One-Point Employee Portal / `emp-internal-transfer` |
| `BRD.md` | Authored — BRD-001 | Seeded from `source-docs/Requirement for SDD.docx`; 11 open questions (Q01–Q11) recorded, none silently resolved |
| `.agent/rules/int-standards.laravel.md` | Created | Defaults only — no Laravel code exists in this repo yet to verify against |
| `.agent/rules/int-standards.nextjs.md` | Created | Defaults only — no Next.js code exists in this repo yet to verify against |
| `specs/emp-internal-transfer.spec.md` | **Draft v1.1 — In Peer Review (Gate 1 re-review)** | AC01–AC18, full API contract (API01–API04), error contract, Next.js consumer contract, UT01–UT18, full traceability; both Gate 1 review passes preserved verbatim plus a "Revision Notes" section dispositioning every item |
| `test_cases/emp-internal-transfer.test_cases.md` | Authored | Broader QA scenarios: API, validation/boundary, auth/RBAC, state-transition, concurrency, contract, Next.js consumer, UI states, accessibility, cross-browser, regression |
| `plans/`, `tasks/`, `decisions/`, `releases/` | Empty | Hold only `.gitkeep`; `plans/`/`tasks/` unblock only after Gate 1 Approved |
| `.ai-context/templates/` | Present — 6 files | Restored 2026-09-30; the two that carried prior-project content (`spec.template.md`, `plan.template.md`) rewritten from the Blueprint's own §11.4/§29 canonical skeletons; two minor stale file-path references cleaned in `release.template.md`/`tasks.template.md`. The other four (`adr`, `hotfix-spec`, `release`, `tasks`) were already generic |
| `.ai-context/inventory/` | Empty (`.gitkeep` only) | Not part of the Blueprint's canonical `.ai-context/` structure (§15) — was prior-project discovery output (an existing codebase's module/API/DB inventory on a different stack); removed again 2026-09-30 after being reintroduced, since there's no code in this repo yet to inventory |
| `.agent/workflows/` | Authored 2026-09-30 | `generate-plan.md`, `generate-tests.md`, `code-review.md` — mandated by Blueprint §15 but missing since the 2026-09-04 cleanup accidentally took the folder itself along with its prior-project-specific content; rebuilt fresh, Laravel/Next.js-neutral |
| `SRS.md`, `architecture.md`, `source-docs/README.md`, `source-docs/proposal-extract.md`, `.agent/rules/int-standards.node.md` | **Removed 2026-09-04** | Prior-project content, confirmed unneeded after Day 2 review; context preserved outside the repo, not in these files |
| `prompt_history.md` | Kept, trimmed 2026-09-30 | Append-only audit trail for this project; its pre-2026-09-03 entries (the prior project's own history) were permanently removed on explicit request — that history is preserved outside the repo, in persistent memory, not here |

## Day 1 execution log — 2026-09-03

- Confirmed with the developer that this repository's existing SDD content was for an unrelated
  prior project and should be repurposed (not treated as this project's real context).
- Archived the prior project's feature artefacts and shared-context files (`constitution.md`,
  `project_context.md`, `BRD.md`, `status.md`, plus 11 specs/1 plan/1 tasks file/2 test-case files)
  to `_archive/empty-floor-legacy/` rather than deleting them.
- Extracted and read the actual business requirement from
  `source-docs/Requirement for SDD.docx` (an SDD developer assessment brief for the Employee
  Internal Transfer Digital Journey).
- Authored BRD-001 from that source, explicitly separating what the source states (business need,
  actors, 8 journey stages, employee capabilities) from what it does not (tenure/eligibility rules,
  effective-date lead time, concurrency limit, SLA, geographic scope, exact stakeholder-assignment
  and failure/rollback rules) — recorded the latter as Q01–Q11 open items, not confirmed decisions.
- Inspected the repository for Laravel/PHP and Next.js conventions: **none found** — no
  `composer.json`, `package.json`, `phpunit.xml`, or any application source tree exists in this
  repository. Recorded this explicitly in both new stack-rule files rather than inventing
  conventions.
- Created `.agent/rules/int-standards.laravel.md` and `.agent/rules/int-standards.nextjs.md` as
  provisional defaults, flagging auth mechanism, routing model, language, and test frameworks as
  `[Open]` Day 5 technical-plan decisions.
- Updated `constitution.md` and `project_context.md` for the One-Point Employee Portal.
- **No `.spec.md`, `.plan.md`, `.tasks.md`, or implementation code was generated.** Day 1 only.

## Day 2 execution log — 2026-09-04

- Read the Day 2 pre-condition set: `BRD.md#BRD-001`, `constitution.md`, `project_context.md`,
  `architecture.md` (confirmed still prior-project content, not applicable), and both
  `.agent/rules/int-standards.*.md` files.
- Authored `specs/emp-internal-transfer.spec.md` (Draft v1.0) from BRD-001: one-paragraph Intent;
  Context section explicitly noting `architecture.md` doesn't apply and no existing portal
  modules exist to link to; 17 Gherkin acceptance criteria (AC01–AC17) covering request
  initiation, validation/rejection, the eligibility gate, manager/HR approval transitions,
  downstream processing and completion, status/pending-action visibility, and authorization
  isolation; a Non-Functional Constraints section citing the constitution's latency/availability/
  PII/auth/rate-limit/coverage baselines; a Frontend/Backend Responsibility Boundary section; and a
  Traceability table mapping every AC (or its explicit absence) back to BRD-001.
- Carried forward BRD-001's open items without silently resolving any of them: no AC was written
  for eligibility-criteria specifics (Q01), minimum effective-date lead time (Q02),
  duplicate/concurrent active-transfer handling (Q03), downstream-step applicability (Q06/Q07),
  rejection/rollback beyond a basic non-approved transition (Q08), withdrawal/cancellation (Q09),
  or the RBAC resolution mechanism for "manager"/"HR" (Q11) — each is called out inline and in a
  dedicated "Open Decisions Carried Into This Spec" table.
- Confirmed auditability is not an approved requirement of this project's `constitution.md` (unlike
  the archived prior project's) and did not assert an audit-logging AC as fact.
- API contract left as the Day 3 placeholder per the spec template; no `.plan.md` or `.tasks.md`
  created; no implementation code written.

**Next:** Gate 1 review of `emp-internal-transfer.spec.md`, then Day 3 (Acceptance Criteria
refinement + API Contract + spec-derived Test Cases) once the developer is ready to proceed.

## Repository cleanup — 2026-09-04 (after Day 2 review)

The developer reviewed `emp-internal-transfer.spec.md`, confirmed intent to proceed to Day 3, and
asked that the leftover prior-project content be removed from the repo now that it's confirmed
unneeded, on condition that its context is preserved (outside the repo, in memory) rather than
lost outright. Before removing anything, the repurposing history and rationale were saved to
persistent memory. Permanently removed: `_archive/empty-floor-legacy/`, `SRS.md`,
`architecture.md`, `inventory/`, `templates/`, `source-docs/README.md`,
`source-docs/proposal-extract.md`, `.agent/rules/int-standards.node.md`. Also updated
`.agent/rules/.agentignore` to drop Node/Prisma-specific ignore patterns and add Laravel/Next.js
equivalents (`vendor/`, `.next/`, `storage/framework/`, `bootstrap/cache/`, `composer.lock`).
`prompt_history.md` was deliberately kept intact — it is an append-only audit trail by its own
rule, and its pre-reset entries are already clearly demarcated as belonging to the prior project.
`releases/` and `decisions/` were kept as they were always empty, generic scaffolding.

## Day 3 execution log — 2026-09-08

- Read the Day 3 pre-condition set: `BRD.md#BRD-001`, `specs/emp-internal-transfer.spec.md`,
  `constitution.md`, both `.agent/rules/int-standards.*.md` files. `architecture.md` no longer
  exists (removed after Day 2, see the cleanup log above) — consistent with there being no
  existing API convention to inherit.
- **Found and fixed two more leftover prior-project artefacts** missed in the prior cleanup pass:
  the root `CLAUDE.md` (still titled for the prior project, pointing at the deleted
  `int-standards.node.md`, still stating the old "blocked pending Phase 1" rule — rewritten for
  this project) and the entire `.agent/workflows/` directory (a Gate 2 reviewer/code-review/
  generate-plan/generate-tests toolchain built specifically for that project, naming its
  reviewers — deleted outright). Recorded in memory for future sessions.
- Finalized `emp-internal-transfer.spec.md`: status set to `In Peer Review (Gate 1)`; added a
  Gate 1 Review block (author recorded, reviewer **Pending Assignment**); replaced the Day 2 API
  Contract placeholder with the full contract — `API01` (Create Transfer Request), `API02` (Get
  Transfer Details), `API03` (Approval/Stage Action) — each with request/response shapes, a
  status/pendingWith vocabulary, and a per-endpoint exception table; added a shared Error Contract
  envelope and a Next.js Consumption Contract section; added a "Contract Gaps" subsection instead
  of inventing reference-data or downstream-detail endpoints; added `UT01`–`UT17` (1:1 with
  `AC01`–`AC17`); extended Traceability to the full BRD→AC→API→UT chain; updated the self-review
  section for the Day 3/Gate 1 readiness check.
- Explicitly flagged as **proposed specification decisions requiring Gate 1 confirmation** (not
  repository facts): the `/api/v1/transfer-requests` base path, the error envelope shape, and the
  403-vs-404 authorization/not-found split — none of these have an existing repository convention
  to defer to.
- Did **not** invent: a duplicate/concurrent-active-transfer exception (Q03), an eligibility-
  criteria-specific rejection beyond the gate's existence (Q01), a minimum-effective-date-lead-time
  validation (Q02), or an audit-log requirement — each remains open exactly as Day 2 left it.
- Created `test_cases/emp-internal-transfer.test_cases.md`: broader QA/developer scenarios across
  11 categories (API, validation/boundary, auth/RBAC, state-transition, concurrency, Laravel
  contract, Next.js integration, loading/error/empty UI, accessibility, cross-browser, regression
  — the last noting there is nothing yet to regress, as this is the first feature). Three "gap
  scenarios" (`VAL-06`, `AUTH-08`, `CONC-03`) are explicitly marked as not asserting behaviour for
  Q02/Q11/Q03 until those resolve.
- No `.plan.md`, `.tasks.md`, migration, controller, service, React component, or production code
  was created.

**Next:** Gate 1 reviewer assignment (name required from the developer — the reviewer must not be
the author), then Gate 1 review itself. Day 4 (per the assessment's own timeline) is Gate 1 peer
review.

## Gate 1 reviewer assignment and author correction — 2026-09-08

The developer named the Gate 1 reviewer: **Sourav Kumar Maity**. Before recording it, a conflict
was flagged and checked: the account's own identity signal matched the proposed reviewer's name
almost exactly, and the spec's recorded author at that point ("Shamik Bhattacharya") didn't match
anything establishing a distinct person — risking a silent self-review. The developer clarified:
the actual **author is Aditya Hazra**; "Shamik Bhattacharya" had been recorded in error at some
earlier point and is now corrected everywhere it appeared (`status.md`, `prompt_history.md`,
`specs/emp-internal-transfer.spec.md`). Sourav Kumar Maity is confirmed as a distinct person from
the author and is recorded as Gate 1 Reviewer in the spec's Gate 1 Review block and in the
Programme status table above. Gate 1 is still **not** Approved — this only assigns the reviewer;
the reviewer's actual decision is a separate, later step.

## Day 4 execution log — 2026-09-15

- Gate 1 Reviewer Sourav Kumar Maity provided a manual review of `BRD.md#BRD-001` and
  `specs/emp-internal-transfer.spec.md`, to be recorded verbatim rather than paraphrased or
  silently resolved.
- Recorded the full review (overall assessment, strengths, seven numbered observations, and
  recommendation) in `specs/emp-internal-transfer.spec.md`'s Gate 1 Review section, dated
  2026-09-15.
- Recorded decision: **`PASS WITH CONDITIONS`** — the reviewer's own wording, not one of the two
  states (`Approved` / `Changes Requested`) the spec's Gate 1 Review block defines. Recorded as-is,
  not mapped onto either state; flagged in the spec for the developer/reviewer to confirm whether a
  third decision state should be formalised. For gating purposes only, treated as **not yet
  Approved** — `plans/`/`tasks/` remain blocked per the Baseline artefacts table below.
- Cross-referenced the reviewer's seven observations against existing tracked items:
  conditional-downstream-activities (obs. 1) → **Q07**; rejection/failure handling (obs. 2) →
  **Q08**; stakeholder ownership of org-info (obs. 4) → **Q06**; RBAC (obs. 5) → **Q11** and the
  BRD's "assume employee + manager + HR only" working-assumption note. Two observations do not map
  to an existing Q-item and are flagged in the spec for the author to consider as new BRD open
  items: Manager→HR sequencing as an enforced state-machine rule vs. source-list ordering
  (obs. 3), and the business rule/source for valid department/location/role options (obs. 6).
  Observation 7 (submission vs. completion confirmation) is noted as partially covered by
  AC01/AC11 but not yet named as two distinct confirmation events.
- Updated spec `Status` to `Gate 1 — Pass with Conditions (revision required before re-review)` and
  the Programme status / Active specs tables above accordingly.
- Did **not** revise the BRD's Open Decisions table or the spec's acceptance criteria — that is the
  developer's follow-up in response to this review, not part of recording it.
- No `.plan.md`, `.tasks.md`, migration, controller, service, React component, or production code
  was created.

**Next:** Developer (Aditya Hazra) revises `BRD.md`/`specs/emp-internal-transfer.spec.md`
addressing the four focus areas the reviewer named (conditional downstream workflow,
rejection/failure handling, Manager/HR sequencing, authorization and stakeholder ownership), then
resubmits for another Gate 1 pass.

## Day 4 execution log (continued) — 2026-09-15, spec-level review

- Same-day second pass by Sourav Kumar Maity (Gate 1 Reviewer), this time against
  `specs/emp-internal-transfer.spec.md` itself (acceptance criteria, API contract, state model)
  rather than the BRD/journey level covered by the first pass above. Recorded verbatim in the
  spec's Gate 1 Review section as "Part 2 — spec-level review."
- Three **new** gaps surfaced that were not tracked by either the BRD's Q01–Q11 or the first review
  pass: (a) **downstream-completion mechanism** — API03 only defines `approve|decline` for
  Manager/HR; no API/authorization mechanism exists for a downstream stakeholder (Payroll/IT/
  Facilities/org-info) to mark their own step complete, so AC11 currently has no implementable path;
  (b) **state-model inconsistency** — the status vocabulary marks `hr_approved` non-terminal
  unconditionally, but AC11 allows direct completion when zero downstream steps apply, which the
  vocabulary table doesn't yet express; (c) **status-visibility scope** — AC12 restricts status
  viewing to non-completed requests, but the BRD source does not state that exclusion; whether
  employees can view completed/rejected request status is unconfirmed.
- Remaining points restate/extend Part 1's observations against the spec's concrete artefacts:
  downstream applicability (Q07), Manager→HR sequencing, RBAC (Q11, plus a new downstream-actor
  authorization angle once clarification (a) is resolved), department/location/role selection
  (business vs. technical split), and employee confirmation (now split into three candidate events:
  submission, approval/rejection outcome, final completion).
- Decision recorded again as `PASS WITH CONDITIONS` (reviewer's wording) — consistent with Part 1;
  gating treatment unchanged (not yet Approved; `plans/`/`tasks/` remain blocked).
- No BRD/spec revision made in this entry — recording the review only, per the developer's
  follow-up responsibility.

**Next:** unchanged — developer revises `BRD.md`/`specs/emp-internal-transfer.spec.md` addressing
both review passes (BRD-level and spec-level) together, then resubmits for Gate 1 re-review.

## Gate 1 revision (v1.1) + workspace fixes — 2026-09-30

Read the full INT SDD Blueprint PDF for the first time this session (previously only the
summarized methodology had been used) — grounded this revision and the workspace fixes below
directly in the actual standard.

**BRD.md revised:**
- Added **Q12** (Manager→HR sequencing — enforced rule or incidental ordering) and **Q13**
  (department/location/role selection business rule), both raised by the Gate 1 review and
  previously only recorded in review prose, not tracked.
- Added a **"Controlled Assumptions Adopted for Spec v1.1"** section: explicit, labelled working
  assumptions for **Q07** (downstream applicability, grounded in which fields the request actually
  changes) and **Q08** (rejection is terminal with nothing to roll back; downstream failure
  surfaces as "pending resolution," not auto-retry/escalate) — adopted because the reviewer's own
  Part 2 observation 3 explicitly invited a controlled assumption in lieu of a confirmed answer,
  and no stakeholder is available in this assessment context to provide one.
- Extended **Q11**'s scope to cover the new downstream-step actors (Payroll/IT/Facilities/org-info),
  not just Manager/HR.

**`specs/emp-internal-transfer.spec.md` revised to Draft v1.1:**
- Added a **"Revision Notes"** section dispositioning every item from both Gate 1 review passes
  (fixed directly / controlled assumption adopted / recorded as new BRD item) — see the spec itself
  for the full table.
- **Fixed directly** (not business questions, spec's own defects): `AC12` no longer excludes
  completed/rejected requests from status visibility (the source never stated that exclusion); the
  `hr_approved` status vocabulary now explicitly branches to `completed` (zero downstream steps) or
  `downstream_processing` (one or more apply), closing the inconsistency with `AC11`.
- **Added:** `AC18` and `API04` (`POST .../downstream-steps/{step}/complete`) — the previously
  nonexistent mechanism for a downstream stakeholder to mark their step complete, without which
  `AC11` had no implementable path. Authorization identity for who holds each downstream role
  remains open (Q11, extended) — the mechanism itself does not.
- Added a **"Confirmation events"** note naming three distinct moments (submission acknowledgement,
  decision-outcome notification, completion confirmation) that were previously conflated.
- Status bumped to `Draft v1.1 — In Peer Review (Gate 1 re-review)`, replacing the non-standard
  "Pass with Conditions" label with the Blueprint's actual §12.3 convention (version-bump on
  revision, binary Approved/Changes-Requested outcome).
- Nothing renumbered or removed — `AC01`–`AC17`/`API01`–`API03`/`UT01`–`UT17` unchanged in place;
  only additions (`AC18`/`API04`/`UT18`) and in-place clarifications.

**Workspace fixes (per this session's Blueprint read and the developer's explicit request to
remove any remaining prior-project context):**
- `.agent/workflows/` — authored fresh (`generate-plan.md`, `generate-tests.md`, `code-review.md`).
  Blueprint §15 lists this folder as mandatory; it went missing during the 2026-09-04 cleanup
  because the only versions of those three files that existed at the time were prior-project-
  specific and were removed along with the folder itself.
- `.ai-context/templates/` — the developer restored all six files from their own source; two
  (`spec.template.md`, `plan.template.md`) still carried prior-project specifics (Prisma/Redis/
  Vitest, `src/domain/` layering, the prior project's default author/reviewer names, Wave/Tier
  language) and were rewritten — `spec.template.md` transcribed directly from the Blueprint's own §11.4/§29
  skeleton plus this project's proven additions (Gate 1 Review block, Open Decisions table,
  Frontend/Backend boundary, Traceability); `plan.template.md` restructured for Laravel/Next.js.
  Two minor stale file-path references (`src/openapi/openapi-document.ts`) cleaned in
  `release.template.md`/`tasks.template.md`; the other three files needed no changes.
- `.ai-context/inventory/` — the developer also restored this folder's original four files
  (module/API/database/dependency inventory of the old Express/Prisma codebase). Confirmed against
  the Blueprint's own §15 file listing that `inventory/` is **not** part of the canonical SDD
  structure at all — it was bespoke to the prior project's legacy-codebase backfill. Removed again;
  left as an empty, explained placeholder rather than repopulating it with anything, since there's
  no code in this repo yet to inventory.

**Next:** developer (Aditya Hazra) resubmits `specs/emp-internal-transfer.spec.md` v1.1 and the
revised `BRD.md` to Sourav Kumar Maity for Gate 1 re-review.
