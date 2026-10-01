# Project Status Board

_Last updated: 2026-10-01 — updated by: Aditya Hazra (Developer)_

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
| Stage | **BRD: Gate 1 PASS** · Spec: **Approved — Gate 1 (v1.2.1)** · Plan: **Technical Plan Draft — Technical Review Required** (2026-09-30) |
| Owner | Developer (Aditya Hazra) |
| Gate 1 Reviewer | **Sourav Kumar Maity** |
| Gate 2 Reviewer | **Subhajit Mukherjee** (assigned 2026-10-01) — also reviews the Day 5 plan and Day 6 tasks |
| SDD chain position | Day 5 (Technical Plan + Architecture) drafted; awaiting technical review before Day 6 (Tasks) |

## Active specs

| Spec ID | Title | Status | Owner | Last Updated | Notes |
|---|---|---|---|---|---|
| `emp-internal-transfer` | Employee Internal Transfer Digital Journey | **Technical Plan Draft** (spec Approved — Gate 1 v1.2.1) | Developer | 2026-09-30 | Spec Gate 1 Approved (Part 5, Sourav Kumar Maity). Day 5 plan: `plans/emp-internal-transfer.plan.md` — architecture, API mapping, logical data model, state model, auth/RBAC, reference data, downstream boundaries, failure/concurrency, security/observability, testing strategy, 11 ADR candidates, open decisions. Raises **CR-01** (contradicting approved test cases AUTH-07/STATE-06/STATE-08 on API03 error precedence), **CR-02** (reference-data endpoint), **CR-03** (API01 idempotency) for the author and reviewer. **Next:** Subhajit Mukherjee (Gate 2 Reviewer) reviews the plan; decide ADR-0001/0002/0004 and agree CR-01 before Day 6 |

## Baseline artefacts

| Artefact | Status | Note |
|---|---|---|
| `constitution.md` | Reset — v1.0 (Provisional) | Fresh for this project; several values `[Open]`/`[Provisional]` pending confirmation |
| `project_context.md` | Current (updated 2026-09-30) | One-Point Employee Portal / `emp-internal-transfer`; "Current state" updated from Day 1 to Day 5 |
| `BRD.md` | **Gate 1 PASS — 2026-09-30** (BRD-001) | Seeded from `source-docs/Requirement for SDD.docx`; open questions Q01–Q13 recorded, none silently resolved; controlled assumptions for Q07/Q08/Q05 (Q05 added in the v1.2 fix cycle). Final review recorded verbatim at the end of the file; carry-forward Q06, Q07/Q08, Q11, Q12, Q13 |
| `.agent/rules/int-standards.laravel.md` | Created | Defaults only — no Laravel code exists in this repo yet to verify against |
| `.agent/rules/int-standards.nextjs.md` | Created | Defaults only — no Next.js code exists in this repo yet to verify against |
| `specs/emp-internal-transfer.spec.md` | **Approved — Gate 1 (v1.2.1, 2026-09-30)** | AC01–AC18, full API contract (API01–API04, `pending_resolution` step status with a defined exit), error contract, Next.js consumer contract, UT01–UT18, full traceability; all four Gate 1 review passes preserved verbatim plus revision notes for v1.1, v1.2 and v1.2.1 |
| `test_cases/emp-internal-transfer.test_cases.md` | Updated for v1.2.1 | Broader QA scenarios: API, validation/boundary, auth/RBAC, state-transition, concurrency, contract, Next.js consumer, UI states, accessibility, cross-browser, regression. v1.2.1 added `AUTH-09`/`10`, `STATE-09`…`13`, `FE-09` (API04 and `pending_resolution`) |
| `plans/emp-internal-transfer.plan.md` | **Draft — Technical Review Required** (2026-09-30) | Day 5 technical plan; every technical choice labelled Fact / Approved / Rule / Proposed / Open / ADR, since no Laravel/Next.js source exists yet |
| `tasks/`, `decisions/`, `releases/` | Empty | Hold only `.gitkeep`. `tasks/` blocked until the plan is reviewed; ADR candidates are listed in the plan (§O), no ADR file written until approved |
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

## Gate 1 final review — BRD — 2026-09-30

- Gate 1 Reviewer Sourav Kumar Maity reviewed the revised `BRD.md` against the earlier Gate 1
  observations. Decision: **PASS for progression to the next SDD stage**. Recorded verbatim in
  `BRD.md` ("Gate 1 Final Review Record — BRD, 2026-09-30"), with a status line under the BRD
  header.
- Confirmed by the reviewer: Q07 recorded as a controlled assumption (Org Info/Payroll/IT/
  Facilities); Q08 handled without unsupported auto-recovery; Q12 recorded as an open decision;
  Q11 extended to downstream actors; Q13 recorded as an open decision; business and technical
  decisions separated; traceability to the source document kept.
- **Carry-forward (must stay explicitly open/assumed):** Q06, Q11, Q12, Q13; Q07/Q08 controlled
  assumptions must be validated before production behaviour is confirmed. No Open Decisions row
  changed status.
- **Scope of this PASS:** BRD only. `specs/emp-internal-transfer.spec.md` v1.1 is still
  `In Peer Review (Gate 1 re-review)`; a pointer note was added to its Gate 1 Review block.
  `plans/`/`tasks/` stay blocked until the spec itself is Approved.
- No `.plan.md`, `.tasks.md`, or production code was created.

**Next:** spec-level Gate 1 re-review of `specs/emp-internal-transfer.spec.md` v1.1, checking the
carry-forward items against API contracts, state transitions, ACs, authorization model,
downstream processing, and test cases (`test_cases/emp-internal-transfer.test_cases.md`).

## Gate 1 re-review — spec v1.1 — 2026-09-30

- Gate 1 Reviewer Sourav Kumar Maity reviewed `specs/emp-internal-transfer.spec.md` v1.1.
  Decision (reviewer's words): **PASS WITH MINOR CONDITIONS**. Recorded verbatim in the spec as
  "Gate 1 Review Comments (Part 3 — v1.1 re-review)". Like the earlier `PASS WITH CONDITIONS`,
  it is treated as **not yet Approved** for gating purposes.
- Six conditions, each checked against the spec text and confirmed:
  1. 🔴 UT08 expects `status: hr_approved`, but AC08 and the status vocabulary treat that state as
     transient (next state is `downstream_processing` or `completed`).
  2. 🔴 Traceability maps AC11 → "API03 (final transition)"; it should show AC11/AC18/API04/UT18.
  3. 🟠 Stale "17 ACs / 17 UTs / API01–API03 / three endpoints" references in the Unit Test
     intro, the Day 3 self-review and the status-vocabulary intro.
  4. 🟠 The Next.js Consumption Contract doesn't cover API04.
  5. 🟡 Q05 domestic-only scope reads as a decision, not as a labelled controlled assumption
     (`BRD.md` has controlled assumptions for Q07/Q08 only).
  6. 🟡 There is no status or `stages[]` representation for the Q08 "pending resolution" failure
     path.
- The reviewer notes that none of these needs a redesign. Once 1–3 in particular are fixed, the
  spec should be ready for the Technical Plan and Task Decomposition.
- Nothing was fixed in the spec during this entry; this entry records the review only. No
  `.plan.md`, `.tasks.md`, or production code was created.

**Next:** Developer (Aditya Hazra) fixes the six conditions (v1.2, with a Revision Notes row for
each), then resubmits. The reviewer records Approved, which unblocks Day 5 (Technical Plan).

## Gate 1 fix cycle — v1.2 — 2026-09-30

Independently verified all six of Sourav Kumar Maity's Part 3 conditions against the actual spec
text before fixing anything — all six confirmed accurate, none overreach. Fixed all six in
`specs/emp-internal-transfer.spec.md`, bumped to `Draft v1.2`:

1. 🔴 `UT08`'s Expected column corrected — no longer claims `status: hr_approved` (transient only);
   now states both branches (`downstream_processing` / `completed`), matching `AC08`.
2. 🔴 Traceability's "journey stage 8" row now names `AC11`+`AC18`, `API04` (the actual completion
   transition) alongside `API03`/`API02`, and `UT11`+`UT18` — no longer implies `API03` alone
   completes a request.
3. 🟠 Stale `17 ACs`/`17 UTs`/`API01–API03`/"three endpoints" corrected to `18`/`18`/`API01–API04`/
   "four endpoints" in the Unit Test Cases intro, the status-vocabulary intro, and the Day 3
   self-review (with a note explaining the numbers were corrected in v1.2, not silently rewritten).
4. 🟠 Next.js Consumption Contract now names API04 throughout, including a `pending_resolution`
   display-state note.
5. 🟡 Q05 (geographic scope) added to `BRD.md`'s Controlled Assumptions section alongside Q07/Q08;
   this spec's Open Decisions row and Out of Scope wording both now say "controlled assumption,"
   not "excluded by default."
6. 🟡 `pending_resolution` added as a `stages[].steps[].status` value (a per-step detail, not a new
   top-level `status`) — representing `BRD.md`'s Q08 assumption. The mechanism that would actually
   set this value (a downstream stakeholder reporting a step un-completable) is **not** defined —
   flagged as a new, explicit Contract Gap rather than invented, since the BRD doesn't specify who
   decides this or how.

**Also self-caught while re-checking (not one of the six):** `UT12` still said "non-completed
status," stale from the same `AC12` widening v1.1 already made — the exact same class of defect as
condition 1. Fixed; `UT13`'s wording aligned to "non-terminal" for precision.

Added "Revision Notes v1.2" to the spec, dispositioning all six conditions, and a "Self-Review
Against Gate 1 Re-Review Readiness (v1.2)" section. No AC/API/UT renumbered — only in-place
corrections plus the one new step-status value and one new Contract Gap row. No `.plan.md`,
`.tasks.md`, migration, controller, service, React component, or production code created.

Added a **"Reviewer guidance for the v1.2 re-review"** subsection to the spec's Gate 1 Review block
(check vs. safe-to-skip, with section locations), plus pointers to it from `BRD.md`'s header and
this board's Active specs row — so the reviewer knows exactly where to look on pull.

**Next:** Resubmit v1.2 to Sourav Kumar Maity for Gate 1 re-review. If he records Approved,
`plans/`/`tasks/` unblock and Day 5 (Technical Plan) can begin.

## Gate 1 re-review (Part 4) — spec v1.2 — 2026-09-30

- Gate 1 Reviewer Sourav Kumar Maity reviewed the author's `specs/emp-internal-transfer.spec.md`
  v1.2 (PR #2). Decision: **`Changes Requested (minor)`**. This is the binary Blueprint §12.3
  state; "pass with conditions" is not formalised as a third state. Recorded as "Gate 1 Review
  Comments (Part 4 — v1.2 re-review)" in the spec.
- The substance of all six Part 3 conditions is accepted, including the condition 6 approach
  (step-level `pending_resolution`). The UT12/UT13 self-caught fix is confirmed.
- Nine mechanical items, P4-01…P4-09:
  - Stale `hr_approved` references in the API02 status enumeration, UT05 and UT10.
  - `test_cases` not updated for v1.2: counts, STATE-03, STATE-05, and no `pending_resolution`
    scenarios.
  - API04 accepts only `pending` steps, so a `pending_resolution` step has no exit and the request
    could never reach `completed`.
  - Record `pendingWith` being single-valued while several steps can be pending as a Contract Gap.
- Blocking rationale: UT05 and UT08 disagree on the result of the same HR approve, and the
  test-first rule would carry that into the first failing tests.
- A reviewer's local draft of v1.2 was discarded in favour of the author's merged version, so that
  approval rests on the author's edits. No `.plan.md`, `.tasks.md` or production code was created.

**Next:** Author (Aditya Hazra) fixes P4-01…P4-09 as v1.2.1, with a Revision Notes row each, and
resubmits. The reviewer verifies them against the checklist in the spec and records **Approved**,
which unblocks Day 5 (Technical Plan).

## Gate 1 fix cycle — v1.2.1 — 2026-09-30

Checked all nine Part 4 items (P4-01…P4-09) against the files before fixing; all nine were
accurate. Fixed in `specs/emp-internal-transfer.spec.md` (bumped to `Draft v1.2.1`) and
`test_cases/emp-internal-transfer.test_cases.md`:

- **`hr_approved` removed as a returned value / expected result:** API02 `status` enumeration
  (P4-01), `UT05` (P4-02), `UT10` (P4-03), `STATE-03` (P4-05), `STATE-05` (P4-06). API03 now has
  an explicit per-action result table, so AC08, API03, UT05, UT08 and STATE-03 all say the same
  thing: HR approve → `downstream_processing` or `completed`.
- **Test-cases file brought up to date (P4-04, P4-08):** 18 UTs / API01–API04 / Q01–Q13; four
  endpoints in AUTH-01/02, CONTRACT-01, UI-04; API04 in the IT-API-03 happy path; new rows
  `AUTH-09`/`10`, `STATE-09`…`13` (including the `pending_resolution` exit and a gap scenario for
  its entry), `FE-09`; `FE-03` corrected to 6 returnable status values; garbled `CONC-02` sentence
  fixed.
- **`pending_resolution` exit (P4-07):** API04 now accepts `pending` or `pending_resolution` steps;
  `AC18` and `UT18` aligned. Removed a duplicate "step never applicable" condition from API04's 409
  row (404 only, per P4-08).
- **`pendingWith` multi-step (P4-09):** recorded as a Contract Gap (non-blocking, Day 5).

Added "Revision Notes v1.2.1" to the spec with a row per item, plus a self-check against the
reviewer's Part 4 approval checklist, verified by searching both files rather than assumed. No
AC/API/UT renumbered. No `.plan.md`, `.tasks.md` or production code created.

**Next:** Resubmit v1.2.1 to Sourav Kumar Maity. If he records **Approved**, `plans/`/`tasks/`
unblock and Day 5 (Technical Plan) can begin.

## Gate 1 final review (Part 5) — spec v1.2.1 — 2026-09-30 — **APPROVED**

- Gate 1 Reviewer Sourav Kumar Maity reviewed `specs/emp-internal-transfer.spec.md` v1.2.1
  (commit `209e9f3`). Every Part 4 approval-checklist item was verified independently against the
  spec and test-cases text. All six pass. Decision: **`Approved`**, recorded as "Gate 1 Review
  Comments (Part 5 — v1.2.1 final review)". It supersedes all earlier Gate 1 decisions.
- Non-blocking editorial note: Context still says "11 open items (Q01–Q11)". Fix it in the next
  spec revision.
- Carried into Day 5, all to stay explicit there:
  - Open: Q06, Q11, Q12, Q13.
  - Controlled assumptions to validate: Q05, Q07, Q08.
  - Contract gaps: `pending_resolution` entry mechanism, `pendingWith` with several steps
    pending, reference-data endpoints, downstream-actor identity mapping.
  - `[Open]` stack decisions: auth mechanism, rate limits, test frameworks.
- No `.plan.md`, `.tasks.md` or production code was created in this entry.

**Next:** Day 5, the Technical Plan (`plans/emp-internal-transfer.plan.md`), authored by the
developer against the Approved spec. It needs its own review before `tasks.md` is derived, and
tests are written and confirmed failing before any implementation.

## Day 5 execution log — Technical Plan + Architecture — 2026-09-30

- **Baseline:** Gate 1-approved spec v1.2.1 (commit `f80e669`), Gate 1-passed BRD-001, v1.2.1
  test cases. No AC, API contract or UT was changed.
- **Repository check:** no Laravel or Next.js source exists, so no convention is claimed as
  verified. Every technical choice in the plan is labelled Fact / Approved / Rule / Proposed /
  Open / ADR.
- **Created** `plans/emp-internal-transfer.plan.md` (Draft — Technical Review Required):
  architecture and layer responsibilities; Laravel and Next.js responsibilities; API01–API04
  mapped to validation/authorization/persistence/effect/response; logical data model
  (TransferRequest, StageDecision, DownstreamStep, TransitionLog; pending action derived, not
  stored; org/reference data external); state model with `hr_approved` never persisted.
- **Integration and failure:** downstream steps are human-completed via API04 only — no system
  integration, queue or scheduler in approved scope. Failure table for validation, auth, DB,
  duplicate, concurrent, stale, partial completion and server failure. One transaction plus a
  row lock per state change; API03/API04 are safe to repeat through state guards; API01 has no
  duplicate protection in the approved contract.
- **Security / observability / performance:** auth on all endpoints; no PII in logs (opaque ids
  only; `reason` never logged); proposed per-user rate limits; correlation id returned in errors;
  per-transition structured logs; p95 < 400 ms and 99.9% targets.
- **Testing strategy** traced BRD → AC → API → UT → plan section; external sources stubbed so tests
  don't wait on ADRs. No new acceptance criteria.
- **ADR candidates:** ADR-0001 auth, 0002 stack baseline/DB/test frameworks, 0003 reference-data
  source, 0004 role/relationship mapping, 0005 integration pattern, 0006 concurrency, 0007
  persisted state model, 0008 transition log, 0009 correlation id, 0010 rate limits, 0011 API01
  idempotency. None written as files — each needs approval first.
- **Raised for the author and Gate 1 reviewer:** CR-01 — approved test cases `AUTH-07` and
  `STATE-08` expect different codes for the same scenario, and `STATE-06` contradicts API03's 403
  rule; the plan proposes an evaluation order but does not edit the approved files. CR-02 —
  reference-data endpoint for the form (spec addition). CR-03 — API01 idempotency (contract
  addition, tied to Q03).
- **Open/business:** Q06, Q11, Q12, Q13, Q03; OD-03 (`pending_resolution` entry), OD-05 (source of
  current dept/loc/role for Q07), OD-06 (manager snapshot vs live), OD-09 (notifications), OD-10
  (client and visibility for manager/HR/downstream actors). **R-01: no technical reviewer
  assigned.**
- Updated `project_context.md`'s "Current state" (was still Day 1).
- **No production code, migrations, controllers, services, components, ADR files or tasks were
  created.**

**Next:** assign a technical reviewer, review the plan, decide ADR-0001/0002/0004 and CR-01, then
Day 6 (Tasks).

## Gate 2 reviewer assigned + activity tracker — 2026-10-01

- **Gate 2 Reviewer: Subhajit Mukherjee** — distinct from the author (Aditya Hazra) and the Gate 1
  reviewer (Sourav Kumar Maity). Recorded in `constitution.md`'s roles table, the plan header and
  this board. Contact details deliberately not recorded (PII).
- Agreed review flow, added to the plan: plan review now (Blueprint §13, "Gate 1 continued", with
  the §12.1 architecture/security sign-off) → `tasks.md` review on Day 6 → Gate 2 per task/PR on
  Days 7–8 → evidence and security checks on Day 9 → final Gate 2 decision on Day 10. Days 6–9 are
  not batched into one review. Plan risk R-01 (no reviewer) resolved.
- Activity tracker (`source-docs/Day-to-day activities-SDD.xlsx`): Day 4 (Gate 1, Complete,
  2 hrs, 9/30/2026) and Day 5 (Planning, Complete — technical review pending, 3 hrs, 9/30/2026)
  rows filled in; the developer's own edits to Days 2–3 kept.

**Next:** Subhajit Mukherjee reviews `plans/emp-internal-transfer.plan.md`. Day 6 starts once that
review passes.
