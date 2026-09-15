# Project Status Board

_Last updated: 2026-09-15 (Day 4) — updated by: Sourav Kumar Maity (Gate 1 Reviewer)_

Single source of truth for **what is happening right now**. Updated the same day by whoever last
touched an artefact.

> **Reset note (2026-09-03):** this board previously tracked an unrelated project ("Empty Floor +
> Circle Tap", 38-spec retro-spec programme). That content — plus the rest of the leftover
> Empty Floor artefacts (`SRS.md`, `architecture.md`, `inventory/`, `templates/`,
> `source-docs/README.md`, `source-docs/proposal-extract.md`, `.agent/rules/int-standards.node.md`)
> — was confirmed not needed and permanently removed from the repo on 2026-09-04 after Day 2
> review; the context is preserved outside the repo instead. This board tracks `emp-internal-transfer`
> only.

---

## Programme status

| Item | State |
|---|---|
| Feature | `emp-internal-transfer` — Employee Internal Transfer Digital Journey |
| Stage | **Gate 1 — Pass with Conditions (revision required)** |
| Owner | Developer (Aditya Hazra) |
| Gate 1 Reviewer | **Sourav Kumar Maity** |
| SDD chain position | Day 4 of the assessment's recommended 10-day timeline complete (Gate 1 review recorded) |

## Active specs

| Spec ID | Title | Status | Owner | Last Updated | Notes |
|---|---|---|---|---|---|
| `emp-internal-transfer` | Employee Internal Transfer Digital Journey | **Gate 1 — Pass with Conditions** | Developer | 2026-09-15 | Gate 1 review recorded by Sourav Kumar Maity (2026-09-15) in the spec's Gate 1 Review block: decision `PASS WITH CONDITIONS`, treated as **not yet Approved** for gating purposes. Conditions focus on conditional downstream workflow (Q07), rejection/failure handling (Q08), Manager→HR sequencing, and authorization/stakeholder ownership (Q06/Q11). **Next:** developer revises BRD/spec addressing the recorded conditions, then a re-review |

## Baseline artefacts

| Artefact | Status | Note |
|---|---|---|
| `constitution.md` | Reset — v1.0 (Provisional) | Fresh for this project; several values `[Open]`/`[Provisional]` pending confirmation |
| `project_context.md` | Reset — Current | One-Point Employee Portal / `emp-internal-transfer` |
| `BRD.md` | Authored — BRD-001 | Seeded from `source-docs/Requirement for SDD.docx`; 11 open questions (Q01–Q11) recorded, none silently resolved |
| `.agent/rules/int-standards.laravel.md` | Created | Defaults only — no Laravel code exists in this repo yet to verify against |
| `.agent/rules/int-standards.nextjs.md` | Created | Defaults only — no Next.js code exists in this repo yet to verify against |
| `specs/emp-internal-transfer.spec.md` | **Gate 1 — Pass with Conditions (2026-09-15)** | AC01–AC17, Out of Scope, NFRs, frontend/backend boundary, full API contract (API01–API03), error contract, Next.js consumer contract, UT01–UT17, full traceability; Gate 1 review comments from Sourav Kumar Maity recorded in the spec's Gate 1 Review section, revision required before re-review |
| `test_cases/emp-internal-transfer.test_cases.md` | Authored | Broader QA scenarios: API, validation/boundary, auth/RBAC, state-transition, concurrency, contract, Next.js consumer, UI states, accessibility, cross-browser, regression |
| `plans/`, `tasks/`, `decisions/`, `releases/` | Empty | Hold only `.gitkeep`; `plans/`/`tasks/` unblock only after Gate 1 Approved |
| `SRS.md`, `architecture.md`, `inventory/`, `templates/`, `source-docs/README.md`, `source-docs/proposal-extract.md`, `.agent/rules/int-standards.node.md` | **Removed 2026-09-04** | Empty Floor content, confirmed unneeded after Day 2 review; context preserved outside the repo, not in these files |
| `prompt_history.md` | Kept | Append-only audit trail; pre-2026-09-03 entries describe the prior project and are clearly marked as such, not deleted |

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
asked that the leftover Empty Floor content be removed from the repo now that it's confirmed
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
- **Found and fixed two more leftover Empty Floor artefacts** missed in the prior cleanup pass:
  the root `CLAUDE.md` (still titled "Empty Floor + Circle Tap API," pointing at the deleted
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
