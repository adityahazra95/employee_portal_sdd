# Plan: Employee Internal Transfer Digital Journey — Technical Plan

| | |
|---|---|
| **Feature** | `emp-internal-transfer` |
| **Title** | Employee Internal Transfer Digital Journey — Technical Plan |
| **Status** | `Draft — Changes Requested (Gate 2 technical review, 2026-10-01)` |
| **Author** | Aditya Hazra (Developer) · **Date:** 2026-09-30 |
| **Technical reviewer** | **Subhajit Mukherjee** (Gate 2 Reviewer; assigned 2026-10-01) — plan review + architecture/security sign-off |
| **Review decision** | **Changes Requested** — 2026-10-01. Architecture/security sign-off **withheld** until B1–B5 are resolved. See "Gate 2 Technical Review" at the end of this file |

## Review Flow (agreed 2026-10-01)

Per Blueprint §13 (plan review is "Gate 1 continued", before task generation), §12.1
(architecture/security sign-off) and §14/§18 (Gate 2 in small, reviewable increments):

| When | Review | Reviewer |
|---|---|---|
| Day 5 (now) | Technical review of this plan, including architecture/security sign-off; decide or accept ADR-0001/0002/0004 | Subhajit Mukherjee |
| Day 5–6 | Agree CR-01 (contradicting approved test cases) — touches Gate 1-approved artefacts | Subhajit Mukherjee + Sourav Kumar Maity (Gate 1) + author |
| Day 6 | Quick review of `tasks.md` before any implementation | Subhajit Mukherjee |
| Days 7–8 | Gate 2 per task / small PR: tests confirmed failing first, then passing; each AC verified against the diff by ID | Subhajit Mukherjee |
| Day 9 | Security checklist, coverage, traceability, Gate 2 evidence pack | Author prepares, Subhajit Mukherjee checks |
| Day 10 | Final Gate 2 decision | Subhajit Mukherjee |

Day 6 tasks are derived only after this plan's review passes (`CLAUDE.md`: tasks come from a
reviewed plan). Days 6–9 are **not** batched into a single review.

**Reviewer: start here.** The decisions that need you are §O (ADR candidates — especially
ADR-0001, 0002, 0004, 0010), §H.3 + §P (CR-01), §I (reference data, CR-02), and the Constitution
Check's open sign-off item. Everything labelled **[Approved]** comes from the Gate 1-approved spec
and shouldn't be re-litigated here.

## Derived From

- `.ai-context/specs/emp-internal-transfer.spec.md` — **Approved, Gate 1 (v1.2.1, 2026-09-30)**,
  commit `f80e669`. This plan does not change any AC, API contract, or UT in it.
- `.ai-context/BRD.md#BRD-001` — Gate 1 PASS (2026-09-30), including its open decisions and
  controlled assumptions.
- `.ai-context/test_cases/emp-internal-transfer.test_cases.md` (v1.2.1).
- `.ai-context/constitution.md`, `.agent/rules/int-standards.laravel.md`,
  `.agent/rules/int-standards.nextjs.md`, INT SDD Blueprint v1.0 (§10, §13, §20, §29).

## How to read this plan — decision classes

The repository contains **no Laravel or Next.js source** (checked 2026-09-30: no `composer.json`,
`package.json`, `artisan`, `next.config.*`, or `*.php` anywhere; only `.agent/`, `.ai-context/`,
`CLAUDE.md`, `README.md`). Nothing below is a "repository convention." Every technical statement
carries one of these labels:

| Label | Meaning |
|---|---|
| **[Fact]** | Verifiable in the repository today |
| **[Approved]** | Fixed by the Gate 1-approved spec, the Gate 1-passed BRD, or the constitution |
| **[Rule]** | A project rule from `int-standards.*.md` — binding, but itself a default, not yet verified against real code |
| **[Proposed]** | This plan's recommendation; needs technical review before tasks rely on it |
| **[Open]** | Undecided; needs an owner's decision (business or technical) |
| **[ADR]** | Significant enough to need an ADR before the dependent task starts (see §O) |

Business rules are never introduced here. Where the design depends on an open business decision,
the dependency is named and the design stays parameterised rather than assuming an answer.

---

## A. Technical Summary

An authenticated employee submits a transfer request (target department/business unit, location,
role, effective date, optional reason). The request moves through manager confirmation, then HR
eligibility validation, then zero or more downstream steps (organisational information, payroll,
IT, facilities), each completed by its responsible stakeholder, until it is `completed` — or it
ends at `manager_declined` / `hr_declined` **[Approved]**.

Technically this is a **single Laravel REST API** that owns all rules, authorization and state,
and a **Next.js client** that renders the approved contract **[Approved: constitution
Architectural Constraints]**. Four endpoints exist (API01–API04) **[Approved]**. All four are
synchronous request/response; nothing in the approved scope requires a queue, scheduler or
external system call **[Proposed]**. Downstream steps are human-completed through API04 — the
spec defines no system integration to Payroll/IT/Facilities, and the constitution forbids adding
one without an ADR **[Approved]**.

Key design choices proposed here:

1. `hr_approved` is **never persisted** — the HR-approve action writes `downstream_processing` or
   `completed` directly, so it can never be returned (spec: "transient only") **[Proposed]**.
2. `pendingWith` and `stages[]` are **derived** from stored state on every read, not stored
   separately, so they can't drift out of sync **[Proposed]**.
3. Every state change runs in **one database transaction with a row lock on the transfer
   request**, which is what makes the `409 already_processed` race behaviour deterministic
   **[Proposed] [ADR]**.
4. Authentication, database engine, reference-data source and the manager-relationship source are
   **not decided** and are recorded as ADR candidates / open decisions rather than assumed.

## B. Architecture

```
┌────────────────────┐   HTTPS, JSON (approved contract, /api/v1)   ┌─────────────────────────────┐
│ Next.js / React    │ ───────────────────────────────────────────▶ │ Laravel REST API            │
│ presentation only  │ ◀─────────────────────────────────────────── │                             │
└────────────────────┘      data envelope / error envelope          │ 1 HTTP: routing, middleware │
                                                                    │ 2 Validation (Form Request) │
                                                                    │ 3 Authorization (Policy)    │
                                                                    │ 4 Transfer domain logic:    │
                                                                    │   lifecycle, applicability, │
                                                                    │   step completion           │
                                                                    │ 5 Persistence (Eloquent)    │
                                                                    │ 6 Serialization (Resources) │
                                                                    └──────────────┬──────────────┘
                                                                                   │
                                          ┌────────────────────────────────────────┼─────────────────────────┐
                                          ▼                                        ▼                         ▼
                                 Relational database               Identity / org-data source      Downstream stakeholders
                                 (engine [Open][ADR])              (auth, manager relationship,     (people using API04 —
                                                                    current dept/loc/role,           no system integration
                                                                    reference data) [Open][ADR]     in approved scope)
```

| Responsibility | Owner | Basis |
|---|---|---|
| Presentation, form, status view, UI states | Next.js | [Approved] |
| API contract (paths, payloads, errors) | Spec | [Approved] |
| Authentication (who is the caller) | Laravel, via the mechanism in ADR-0001 | [Open][ADR] |
| Authorization (may this caller do this, on this request) | Laravel Policies/Gates | [Rule] |
| Validation (shape, required fields, reference data) | Laravel Form Requests | [Rule] |
| Business logic (state machine, Q07 applicability, completion) | Laravel domain/service layer | [Rule] |
| Persistence | Laravel Eloquent / query builder, migrations | [Rule] |
| Response shaping | Laravel API Resources | [Rule] |
| Reference data and org/identity facts | External source, read by Laravel | [Open][ADR] |
| Observability (logs, correlation ID, metrics) | Laravel (server), Next.js (client errors only, no PII) | [Proposed] |

Layer names follow `int-standards.laravel.md` **[Rule]**. No class names are fixed by this plan;
they will follow whatever structure the Laravel scaffold establishes (Day 6+).

## C. Laravel Backend

| Concern | Responsibility | Basis |
|---|---|---|
| **Validation** | API01: four required fields present; `effectiveDate` is a structurally valid ISO date (no lead-time rule, Q02 open); department/location/role ids exist and are currently selectable (source per ADR-0003). API03: `decision` ∈ {`approve`,`decline`}. API04: `step` ∈ the four values. Errors → `400 validation_error` / `invalid_reference_data` / `malformed_payload` with `errors[]` | [Approved] contract; [Rule] Form Requests |
| **Authorization** | Every route authenticated. Policies decide by **relationship** (requester, the requester's manager, HR role, downstream-step role), never in controllers. Evaluation order in §H.3 | [Rule]; order [Proposed] |
| **Transfer creation (API01)** | Create the request for the caller only (no on-behalf submission). Snapshot the employee's current department/location/role and resolve the manager at submission (see §F, §I). Status `submitted` | [Approved] behaviour; snapshot [Proposed] |
| **Lifecycle / state management** | A single domain component owns all transitions in §G; nothing else writes `status` | [Proposed] |
| **Stage actions (API03)** | Infer the pending stage from current status; apply manager or HR decision. HR approve evaluates Q07 applicability and creates step rows in the same transaction | [Approved] behaviour; mechanics [Proposed] |
| **Downstream completion (API04)** | Complete one step (`pending` or `pending_resolution` → `complete`); if no applicable step remains open, move request to `completed` in the same transaction | [Approved] |
| **Status / timeline (API02)** | Build `status`, `pendingWith`, `stages[]` from stored rows on read (§F, derivation rules) | [Approved] shape; derivation [Proposed] |
| **Downstream orchestration** | Create step records on HR approve. No outbound calls in approved scope | [Proposed] (§J) |
| **Persistence** | Transfer request, stage decisions, downstream steps, transition log (§F) | [Proposed] |
| **Errors** | One exception-to-envelope mapping produces the approved error envelope; never stack traces/SQL | [Approved] envelope; [Rule] Laravel handler |
| **Concurrency / idempotency** | Row lock + state guard per action; `409` codes per §L | [Proposed][ADR] |
| **Transactions** | One transaction per state-changing request (§L) | [Proposed] |
| **Audit / events** | Internal append-only transition log (not exposed by API; spec says audit is not a requirement) | [Proposed][ADR] — see ADR-0008 |
| **Rate limiting** | Per-user limits per endpoint (§M) | [Proposed] values; decision required by constitution |

## D. Next.js Frontend

| Area | Responsibility | Constraint |
|---|---|---|
| **Transfer form** | Department/business unit, location, role selectors; effective date; optional reason; submit | Field ergonomics only (e.g. date picker); validity decided by API |
| **Reference-data consumption** | Populate selectors from the reference-data source | **Blocked on ADR-0003 / CR-02**: no approved endpoint exists; see §I |
| **Validation feedback** | Bind `errors[]` from `validation_error` / `invalid_reference_data` to fields | Doesn't duplicate server rules |
| **Submission** | Disable submit while in flight (UI-01) — the only duplicate-submit protection in approved scope, see §L | UX courtesy, not enforcement |
| **Status / timeline** | Render API02 `status`, `stages[]`, `steps[]` | Never recomputes state; `hr_approved` is never received; unknown value → "unexpected state" (FE-03) |
| **Pending actions** | Show `pendingWith`; for downstream progress render `stages[].steps[]` (authoritative, since `pendingWith` names only one step) | Per Contract Gap P4-09 |
| **Loading / error / empty** | Per endpoint: loading, per-`errorCode`-family error state, "no timeline yet" after submit, network-failure state distinct from `500` (UI-01…UI-04); `pending_resolution` as "needs attention" (FE-09) | |
| **Authorization-aware UI** | Hide approve/decline or complete actions the viewer can't take, using `pendingWith` and the viewer's known role | Courtesy only; API still enforces (FE-08) |
| **Accessibility / responsiveness** | Labelled inputs, errors programmatically linked, keyboard operable, announced results; usable at mobile width (A11Y-01…04, XBROWSER-02) | |
| **Security** | No PII or tokens in console/analytics; token handling per ADR-0001 (no `localStorage` without a recorded decision) | [Rule] int-standards.nextjs |

Routing model (App vs Pages Router), language and data-fetching library are **[Open]** (ADR-0002)
— the plan works with either.

**Manager / HR / downstream stakeholder screens:** the spec rules out dedicated stakeholder working
screens, but API03/API04 must be invoked by *some* client. Whether the Next.js app exposes a
minimal action view for these actors (e.g. approve/decline on the request's own page) is **[Open]**
— see OD-10.

## E. API Mapping

All four endpoints are exactly as approved; nothing here changes paths, payloads, status codes or
error codes.

| API | Backend responsibility | Validation | Authorization | Persistence | Downstream effect | Response |
|---|---|---|---|---|---|---|
| **API01** `POST /api/v1/transfer-requests` | Create request for caller; snapshot current org data; resolve manager | 4 required fields; ISO date; reference ids exist + selectable → `400` codes | Authenticated employee; self only | Insert request (`submitted`) + manager-stage row (`pending`) + transition log, one transaction | None | `201`, `data` with `transferRequestId`, `status: submitted`, `pendingWith: manager` |
| **API02** `GET /api/v1/transfer-requests/{id}` | Load request, derive `pendingWith`/`stages[]` | Path id format; unknown → `404` | Requester, requester's manager, or HR role; else `403 forbidden` (existence not hidden) | Read only | None | `200`, `data` (six returnable statuses) |
| **API03** `POST …/{id}/actions` | Infer pending stage; apply decision; on HR approve evaluate Q07 and create steps | `decision` enum → `400` | Relationship + stage stakeholder, order in §H.3 → `403 forbidden_wrong_stakeholder` | Lock request row; update status + stage row; insert step rows (HR approve); transition log | HR approve with ≥1 applicable step creates `pending` step rows | `200`, `data`; status per API03 result table |
| **API04** `POST …/{id}/downstream-steps/{step}/complete` | Complete step; if last open step, complete request | `step` enum; step exists on request else `404` | Caller holds the role for `{step}` (mapping per ADR-0004) → `403` | Lock request row; update step; maybe update status; transition log | Completes one downstream step; may finish the journey | `200`, `data` |

All four: `401 unauthenticated`, `429 rate_limited` (thresholds §M), `500 internal_error`, error
envelope with `correlationId` (§M) **[Approved]**.

## F. Logical Data Model

Logical only — no migrations, table names, or column types are fixed here **[Proposed]**.

| Entity | Key fields | Relationships | Ownership | Sensitivity |
|---|---|---|---|---|
| **TransferRequest** | opaque public id (`transferRequestId`, non-sequential); requester ref; manager ref (resolved at submission, see OD-06); target department/location/role refs; **snapshot of current** department/location/role at submission; `effectiveDate`; `reason` (nullable); `status` (one of the six persisted values); `submittedAt`; `updatedAt`; lock version | 1–n StageDecision, 0–n DownstreamStep, 1–n TransitionLog | This feature | **High**: identifies an employee's intended move; `reason` is free text that may contain personal information — never logged |
| **StageDecision** | request ref; stage (`manager_confirmation`, `hr_eligibility`); outcome (`pending`, `approved`, `declined`); decided-at; decided-by ref (internal only, not in API — spec Auditability) | n–1 TransferRequest; unique (request, stage) | This feature | Medium |
| **DownstreamStep** | request ref; step (`org_info`, `payroll`, `it`, `facilities`); status (`pending`, `complete`, `pending_resolution`); completed-at; completed-by ref (internal) | n–1 TransferRequest; unique (request, step); rows exist only for applicable steps | This feature | Medium |
| **TransitionLog** | request ref; from-status; to-status; action; actor ref (internal id only); occurred-at; correlation id | n–1 TransferRequest; append-only | This feature | Medium — no names, emails or `reason` text |
| **Pending action** | *Not stored* — derived from status + StageDecision + DownstreamStep | — | — | — |
| **Employee reference** | internal employee id from the identity source | Requester and manager refs point here | **External** (identity source, ADR-0001/0004) | High — only ids stored by this feature |
| **Department / Business Unit** | id; display name; selectable flag | Target and snapshot refs | **External** (ADR-0003; business rule Q13) | Low |
| **Location** | id; display name; selectable flag | as above | **External** (ADR-0003; Q13) | Low |
| **Role / Job Position** | id; display name; selectable flag | as above | **External** (ADR-0003; Q13) | Low |
| **Downstream processing state** | The request's `downstream_processing` status + its DownstreamStep rows | — | This feature | — |

**Derivation rules for API02 [Proposed]:**

- `stages[manager_confirmation].status` = its StageDecision outcome.
- `stages[hr_eligibility].status` = its StageDecision outcome, or `not_applicable` if the manager
  declined.
- `stages[downstream_processing].status` = `not_applicable` (declined anywhere, or HR approve found
  zero steps), `pending` (not reached yet), `in_progress` (≥1 step open), `complete` (all steps
  complete).
- `pendingWith`: `submitted` → `manager`; `manager_approved` → `hr`; `downstream_processing` → the
  **first open step in the fixed order `org_info` → `payroll` → `it` → `facilities`**
  (resolves Contract Gap P4-09 within the approved single-value contract); terminal → `none`.

**Why snapshot current org data at submission [Proposed]:** the Q07 assumption applies a step
when the request *changes* department, role or location, which needs the employee's current
values. Snapshotting at submission makes HR-approve deterministic and testable. Where those current
values come from is **[Open] OD-05**.

## G. State / Lifecycle Model

Persisted request statuses: `submitted`, `manager_approved`, `manager_declined`, `hr_declined`,
`downstream_processing`, `completed` **[Approved]**. `hr_approved` exists only in the vocabulary
and is never persisted **[Proposed]**. Step statuses: `pending`, `complete`, `pending_resolution`
**[Approved]**.

| Current state | Actor | Action | Next state | Invalid action behaviour |
|---|---|---|---|---|
| — | Employee (self) | API01 submit | `submitted`; manager stage `pending` | Missing/invalid fields → `400`; no record created |
| `submitted` | Requester's manager | API03 approve | `manager_approved` | Another actor → `403` (§H.3) |
| `submitted` | Requester's manager | API03 decline | `manager_declined` (terminal) | as above |
| `manager_approved` | HR role | API03 approve | Evaluate Q07: ≥1 step → `downstream_processing` + `pending` step rows; 0 steps → `completed` | Manager acting again → see §H.3 / CR-01 |
| `manager_approved` | HR role | API03 decline | `hr_declined` (terminal); no step rows | as above |
| `downstream_processing` | Step's role holder | API04 complete (`pending` or `pending_resolution`) | Step → `complete`; if no open step remains → `completed` | Wrong role → `403`; step not on request → `404`; step already `complete` → `409 invalid_state_transition` |
| `downstream_processing` | *[Gap]* | Step reported un-completable | Step → `pending_resolution`; request stays `downstream_processing` | **Entry mechanism undefined** — OD-03 |
| `downstream_processing` | Manager / HR | API03 | — | `409 invalid_state_transition` (not awaiting a human decision) |
| Any terminal (`manager_declined`, `hr_declined`, `completed`) | Anyone with a relationship | API03 / API04 | — | `409 invalid_state_transition` |
| Non-`downstream_processing` | Anyone | API04 | — | `409 invalid_state_transition` (or `404` if the step doesn't exist on the request) |
| Any, concurrent | Two actors / double-click | Same action | First commits; second sees a changed state under the row lock | `409 already_processed` (§L) |

**Retry / partial completion:** partial completion is the normal `downstream_processing` state —
some steps `complete`, others `pending`/`pending_resolution`. There is **no automatic retry** of
any step (Q08 controlled assumption: no auto-retry, no auto-escalation) **[Approved]**. A stuck step
exits only through API04 once resolved outside the system **[Approved]**. Rejection after
downstream work began cannot happen in the approved model (rejections only occur at manager/HR
stages, before any step exists), so no rollback is needed **[Approved: Q08 assumption]**.

## H. Authentication & Authorization

### H.1 Authentication — [Open][ADR-0001]

No mechanism is established. The requirement says the organisation already **has** a One-Point
Employee Portal, which implies an existing identity provider/session that this feature should
probably reuse rather than replace — but nothing about it is in this repository. Options:
(a) reuse the existing portal's SSO/IdP (OIDC/SAML) with the Laravel API validating its tokens;
(b) Laravel Sanctum SPA cookie authentication (first-party Next.js on the same parent domain);
(c) Sanctum personal access tokens; (d) Passport OAuth2. **Proposed direction:** (a) if the
existing portal's IdP is reachable, otherwise (b). Either way: httpOnly cookies or in-memory tokens,
never `localStorage` without a recorded decision **[Rule]**.

### H.2 Authorization — per actor

| Actor | May view (API02) | May act | Determined by | Basis |
|---|---|---|---|---|
| Employee | Own requests only | API01 (self); nothing else | Authenticated id = requester ref | [Approved] AC14 |
| Manager | Requests where they are the requester's manager | API03 while `pendingWith: manager` | Manager ref resolved from the org source (OD-06) | [Approved] AC15; source [Open] Q11 |
| HR | Any request (spec: "a user holding the HR role") | API03 while `pendingWith: hr` | HR role from identity source | [Approved] AC16; source [Open] Q11 |
| Downstream stakeholder | **Not granted by the spec** — API02's permitted viewers are requester, manager, HR only | API04 for the step matching their role | Step-role mapping (ADR-0004) | [Approved] API04; mapping [Open] Q11 |
| Anyone else | Nothing → `403 forbidden` | Nothing | — | [Approved] AC17 |

Downstream stakeholders can complete a step (API04) but cannot view the request (API02) under the
approved contract. That is consistent but awkward for a real user — see OD-10.

### H.3 Evaluation order for API03 — [Proposed][CR-01]

The approved test cases conflict with each other here: `AUTH-07` expects a manager acting on a
`manager_approved` request to get `403`, while `STATE-08` expects `409` for the same scenario; and
`STATE-06` (HR acting on a `submitted` request) expects `409`, where API03's contract row says `403`
for "caller is not the stakeholder the currently-pending stage requires." No single ordering
satisfies all three. Proposed order (fully within the approved codes):

1. Not authenticated → `401`
2. Request not found → `404`
3. Caller has no relationship to the request → `403 forbidden_wrong_stakeholder`
4. Request terminal, or not awaiting a human decision (`downstream_processing`) → `409 invalid_state_transition`
5. Caller's own stage was already decided (e.g. manager on a `manager_approved` request) → `409 already_processed`
6. Caller is not the stakeholder for the currently pending stage (e.g. HR on `submitted`, requester) → `403 forbidden_wrong_stakeholder`
7. Body invalid → `400 validation_error`
8. State changed between read and locked write → `409 already_processed`

Effect on approved test cases: `STATE-08` (manager_approved case) → `already_processed`; `AUTH-07`
→ `409 already_processed`; `STATE-06` → `403`. `CONC-01`/`CONC-02` are satisfied. **This needs the
spec/test-case owner's and reviewer's agreement (CR-01) before Day 6 derives tasks** — it is not
applied to the approved files here.

API04 order: `401` → `404` (request or step not on it) → role check `403` → `409` (not
`downstream_processing`, or step already `complete`) → race `409 invalid_state_transition`.

## I. Reference Data — [Open][ADR-0003]

| Data | Technical source | Status |
|---|---|---|
| Department / business unit | Unknown — likely the organisation's HRIS/org master | [Open] |
| Location | Unknown — likely HRIS or facilities master | [Open] |
| Role / job position | Unknown — likely HRIS job catalogue | [Open] |
| Employee's current dept/loc/role (needed by Q07) | Unknown — same HRIS | [Open] OD-05 |
| Manager relationship | Unknown — HRIS reporting line | [Open] OD-06 / Q11 |

Options: (a) read from the existing portal/HRIS API at request time; (b) periodic sync into local
read-only tables; (c) locally maintained tables (admin-managed). **Proposed direction:** (a) or
(b) — the portal must not become the system of record for org data (spec Out of Scope: "replacement
of downstream systems"). (b) needs a scheduler, which is a new mechanism → ADR.

**Contract impact:** the Next.js form cannot be built without an endpoint for selectable values.
None is approved (spec Contract Gap). Adding one is a **spec change (CR-02)** that must go through
Gate 1 review, not something this plan adds. What values are selectable remains business decision
**Q13**.

**Impact if unresolved:** API01 validation (AC04) and the form (D) cannot be implemented or tested
against real data; tests can use a stubbed source. **Owner:** HR/IT system owner (business), TL
(technical).

## J. Downstream Integration

The approved scope has **no system-to-system integration**. Each downstream step is a work item a
human completes via API04 **[Approved]**; adding a system integration needs an ADR **[Approved:
constitution]**.

| Step | Trigger | Data | Outcome | Failure | Retry | Ownership |
|---|---|---|---|---|---|---|
| Organisational information | HR approve, always applies (Q07 assumption) | Request id, target dept/loc/role, effective date | Step `complete` via API04 | Stakeholder can't complete → `pending_resolution` (entry mechanism OD-03) | None automatic; manual, then API04 | **Owner team [Open] Q06** |
| Payroll | HR approve, if dept/BU or role changes (Q07) | as above | as above | as above | as above | Payroll team; role mapping ADR-0004 |
| IT | HR approve, if dept/BU or location changes (Q07) | as above | as above | as above | as above | IT team; role mapping ADR-0004 |
| Facilities | HR approve, if location changes (Q07) | as above | as above | as above | as above | Facilities team; role mapping ADR-0004 |

| Mode | Used here | Notes |
|---|---|---|
| Synchronous | Yes — all four APIs | Step creation happens inside the HR-approve transaction |
| Asynchronous | **No** in approved scope | Would be needed only for system integrations or notifications → ADR-0005 |
| Manual | **Yes** — every downstream step | Completed through API04 |

**Gap — how stakeholders learn a step is waiting:** the spec has no notification and no work-queue
endpoint (both out of scope; SLA/reminders are Q04). Without one, a step can sit `pending`
unnoticed. **[Open] OD-09** (business: is notification in v1?).

## K. Failure & Reliability

| Failure | Detection | User behaviour | Persisted state | Retry | Recovery owner |
|---|---|---|---|---|---|
| Validation failure | Form Request rules | `400` with field `errors[]` | None created/changed | User corrects and resubmits | User |
| Authorization failure | Policy (§H.3) | `401`/`403`, generic message | Unchanged | No | — |
| Database failure | Exception in transaction | `500 internal_error` with `correlationId` | Transaction rolled back — unchanged | User may retry; state guard prevents double effect | Ops (via correlation id) |
| Duplicate submission (API01) | **Not detected** — no approved rule (Q03), no approved idempotency key | Second request created if submit sent twice | Two requests | — | UI-01 disables resubmit; real fix CR-03 / Q03 |
| Concurrent action (API03/API04) | Row lock + state re-check | Loser gets `409 already_processed` / `invalid_state_transition` | Exactly one change | Client refreshes via API02 | — |
| Stale action (acting on old screen) | State guard | `409` code per §H.3 | Unchanged | Refresh | User |
| Downstream timeout / rejection | Not applicable in approved scope — no system call exists | — | — | — | — |
| Step can't be completed | Human judgement (entry OD-03) | Step shows `pending_resolution`; request still in progress | Step `pending_resolution` | Manual resolution, then API04 | Step owner team |
| Retry exhaustion | Not applicable — no automatic retry (Q08) | — | — | — | — |
| Partial completion | Normal state | Per-step progress in `stages[].steps[]` | Mixed step statuses | — | Step owners |
| Unexpected server failure | Global exception handler | `500`, generic message, `correlationId` | Rolled back | Retry safe (state guard) | Ops |

## L. Transactions / Concurrency / Idempotency — [Proposed][ADR-0006]

**Atomic boundaries** — one transaction per state-changing request:

| Operation | Inside one transaction |
|---|---|
| API01 | Insert request + manager StageDecision (`pending`) + TransitionLog |
| API03 manager | Lock request → verify state/stage → update status + StageDecision + TransitionLog |
| API03 HR approve | Lock request → verify → evaluate Q07 → update status (`downstream_processing` or `completed`) + StageDecision + insert DownstreamStep rows + TransitionLog |
| API03 HR decline | Lock request → verify → update status + StageDecision + TransitionLog |
| API04 | Lock request → verify → update DownstreamStep → if none open, update status to `completed` → TransitionLog |

**Concurrency:** pessimistic row lock (`SELECT … FOR UPDATE` on the transfer request) for every
state change. Two completions of the *last two* steps can't both miss each other, because both
serialise on the request row. Alternative: optimistic version column with conditional update and
retry. Lock is proposed because contention per request is tiny (a handful of actors) and it makes
the "exactly one wins" behaviour simple to reason about and test.

**Idempotency:**
- API03/API04 are naturally safe to repeat: the second identical call fails the state guard and
  returns `409` with no second effect.
- **API01 has no protection** in the approved contract. An `Idempotency-Key` header would be a
  contract addition → **CR-03** (spec change), and the underlying business rule (one active
  request at a time?) is **Q03**. Until then, only the UI disables double submit.

## M. Security / Observability / Performance

| Area | Approach | Basis |
|---|---|---|
| Authentication | Required on all four endpoints; mechanism ADR-0001 | [Approved]/[Open] |
| Authorization | Policies by relationship (§H); no controller-level checks | [Rule] |
| Input validation | Form Requests on every body/param; reference ids checked against source | [Rule] |
| PII protection | Never log names, emails, `reason` text, or target dept/loc/role values; logs carry opaque ids only. Frontend: no PII in console/analytics | [Approved] constitution |
| Secrets | Environment/config only (`.env` via Laravel config, never committed); none in plan/prompts | [Approved] constitution |
| Secure errors | Approved envelope only; no stack traces, SQL, paths; `APP_DEBUG` off outside local | [Approved] |
| Rate limiting | Per authenticated user: API01 **10/min**, API02 **60/min**, API03 **20/min**, API04 **30/min**; `429 rate_limited` with retry-after | **[Proposed] values**; constitution requires an explicit decision — needs TL approval |
| Correlation IDs | Accept an inbound `X-Request-Id` or generate one; attach to every log line; return it as the error envelope's `correlationId` | [Proposed][ADR-0009] |
| Lifecycle logging | One structured log line per transition: request id, from→to, action, actor internal id, correlation id | [Proposed] |
| Integration logging | Not applicable — no outbound calls | — |
| Monitoring | Per-endpoint latency (p95), error rate by `errorCode`, count of steps in `pending_resolution`, age of oldest `pending` step | [Proposed]; tooling [Open] |
| Performance | p95 < 400 ms, 99.9% availability [Approved, Provisional]. Each endpoint is a small number of indexed reads/writes on one request; indexes on requester ref, manager ref, status. Reference-data lookups are the latency risk if read live (ADR-0003) — cache if option (a) is chosen | [Approved] targets; design [Proposed] |

## N. Testing Strategy

Frameworks are **[Open]** (ADR-0002): Laravel ships with PHPUnit; Pest is the alternative;
frontend framework unset. Test-first is mandatory: each task's tests are written from the spec and
seen failing before implementation **[Approved]**. Coverage floor 80% on changed files
**[Approved, Provisional]**.

| Layer | What | Source of cases |
|---|---|---|
| Laravel feature/API tests | Each endpoint's success shape and every exception row | UT01–UT18; IT-API-01…04; CONTRACT-01…05 |
| Validation | Required fields, reference ids, date format, `decision`/`step` enums | UT03, UT04; VAL-01…09 |
| RBAC | Each actor × each endpoint, including relationship and stage checks | UT14–UT17; AUTH-01…10 (AUTH-07 pending CR-01) |
| State transitions | Every row of §G | UT05–UT11, UT18; STATE-01…13 (STATE-06/08 pending CR-01) |
| Concurrency / idempotency | Parallel requests on one transfer; double-click; last-two-steps race | CONC-01, CONC-02; plus a last-two-steps race test (technical, no new AC) |
| Downstream | Q07 applicability parameterised over which fields change; `pending_resolution` exit | UT08, UT18; STATE-12 (STATE-13 is a gap scenario) |
| Unit (domain) | Transition function, applicability evaluation, `pendingWith` derivation | Derived from AC08, AC10, AC11, AC13, AC18 |
| Next.js API consumption | Renders contract shapes; binds `errors[]`; handles each error family | FE-01…09 |
| Loading / error / empty | UI-01…04 | |
| Accessibility | A11Y-01…04; XBROWSER-01…02 | |

External sources (identity, reference data, manager relationship) are stubbed behind interfaces in
tests, so tests don't wait on ADR-0001/0003/0004 **[Proposed]**.

### Traceability

| BRD-001 | AC | API | UT | Plan section |
|---|---|---|---|---|
| §3 capabilities, stage 1 | AC01–AC04 | API01 | UT01–UT04 | C, E, F, I, K, L |
| Stage 3 (eligibility gate) | AC05 | API03 | UT05 | C, G, H.3 |
| Stage 2 (manager) | AC06, AC07 | API03 | UT06, UT07 | G, H.3, L |
| Stage 3 (HR) | AC08, AC09 | API03 | UT08, UT09 | F (derivation, snapshot), G, L |
| Stages 4–7 (downstream) | AC10, AC18 | API02, API04 | UT10, UT18 | F, G, J, L |
| Stage 8 (completion) | AC11 | API03 (zero steps), API04 | UT11, UT18 | G, L |
| §3 status / pending view | AC12, AC13 | API02 | UT12, UT13 | D, F (derivation) |
| Actors / RBAC | AC14–AC17 | API02, API03, API04 | UT14–UT17 | H |
| Q03, Q09 (no AC) | — | — | — | L (CR-03), out of scope |

No acceptance criterion is added. The last-two-steps race test and the domain unit tests are
technical tests of existing ACs, not new scope.

## O. ADR Candidates

No ADR files are created by this plan; each needs its owner's approval first.

| ADR | Decision | Reason | Options | Proposed direction | Approval needed |
|---|---|---|---|---|---|
| **ADR-0001** | Authentication mechanism | Constitution `[Open]`; blocks every endpoint | Existing portal SSO/IdP; Sanctum SPA cookie; Sanctum tokens; Passport | Reuse existing portal IdP if available, else Sanctum SPA cookie | TL + Security |
| **ADR-0002** | Stack baseline: Laravel/PHP versions, Next.js version + router, language, test frameworks, database engine | Constitution `[Open]`; nothing scaffolded | Current LTS/stable of each; PHPUnit vs Pest; App vs Pages Router; MySQL/MariaDB vs PostgreSQL | Decide once at scaffold time and record in the int-standards files | TL |
| **ADR-0003** | Reference-data and current org-data source | Needed by AC04 validation, the form, and Q07 | Live read from HRIS/portal; scheduled sync; local tables | Live read with caching, or sync — never local system of record | TL + HR/IT system owner |
| **ADR-0004** | Identity/role mapping (manager relationship, HR role, downstream-step roles) | Q11; needed by every authorization check | Roles/groups from IdP; HRIS reporting lines; local role table | Take from the same source as ADR-0001/0003 | TL + HR/IT |
| **ADR-0005** | Integration pattern (manual vs system integration, sync vs async) | Constitution forbids new integrations/queues without ADR | Manual via API04 only; async queue to downstream systems; notifications | Manual only for v1 — confirm scope | TL + business owner |
| **ADR-0006** | Concurrency control | Deterministic `409` behaviour and race safety | Pessimistic row lock; optimistic version + conditional update | Pessimistic row lock per request | TL |
| **ADR-0007** | Persisted state model (`hr_approved` never stored; `pendingWith`/`stages` derived) | Guarantees contract invariants | Store `hr_approved` + derived fields; derive on read | Don't store transient/derived values | TL |
| **ADR-0008** | Transition log / audit | Spec says audit is not required; log helps integrity and support | No log; internal append-only log; full audit trail (needs constitution change) | Internal append-only log, not exposed, no PII | TL (constitution owner if it becomes audit) |
| **ADR-0009** | Error / correlation-id strategy | Envelope's `correlationId` is conditional in the spec | Accept `X-Request-Id` or generate; none | Accept-or-generate, return in every error | TL |
| **ADR-0010** | Rate-limit thresholds | Constitution requires explicit decision | Values in §M; none | §M values | TL |
| **ADR-0011** | API01 idempotency | Duplicate submission unprotected | UI only; `Idempotency-Key` header (contract change, CR-03); business rule via Q03 | UI only now; revisit with Q03 | TL + reviewer (contract change) |

## P. Risks & Open Decisions

### Business decisions

| ID | Decision | Impact | Owner |
|---|---|---|---|
| Q06 | Who owns the org-info update step | Can't assign API04 role for `org_info` | PM/HR |
| Q11 | Who can act as manager / HR / downstream roles, delegation | Every authorization rule depends on it | IT/HR |
| Q12 | Manager→HR strictly sequential (working assumption) | State model assumes sequential | PM/HR |
| Q13 | What department/location/role values are selectable | AC04 validation, the form | HR/IT system owner |
| Q05, Q07, Q08 | Controlled assumptions — validate before production | Q07 drives which steps are created | PM/HR |
| Q03 | One active request at a time? | Duplicate protection (CR-03) | PM/HR |
| OD-03 | How a step enters `pending_resolution` (who decides, how) | STATE-13 untestable; state has no entry | PM + step owners |
| OD-09 | Are stakeholders notified of pending steps in v1 (relates to Q04) | Steps may sit unnoticed | PM |

### Technical decisions

| ID | Decision | Impact | Owner |
|---|---|---|---|
| ADR-0001…0011 | See §O | Most block specific Day 6 tasks | TL |
| OD-05 | Source of the employee's current dept/loc/role (Q07 evaluation) | HR-approve can't evaluate applicability without it | TL + HR/IT |
| OD-06 | Resolve manager at submission (snapshot) vs at action time | Affects AC15 behaviour if the reporting line changes mid-request | TL + HR |
| R-01 | ~~No technical reviewer assigned~~ **Resolved 2026-10-01:** Subhajit Mukherjee (Gate 2 Reviewer) reviews this plan. Technical Lead is still unassigned in the constitution | Plan review can proceed | — |
| R-02 | No scaffold exists; all conventions are unverified defaults | Day 6 tasks must start with scaffolding + ADR-0002 | TL |

### Mixed decisions (need both business and technical owners)

| ID | Decision | Impact | Owner |
|---|---|---|---|
| **CR-01** | API03 evaluation order vs contradicting test cases (`AUTH-07`, `STATE-06`, `STATE-08`) — §H.3 | Approved test cases can't all pass; must agree before tests are written | Author + Gate 1 reviewer |
| **CR-02** | Reference-data endpoint(s) for the form — spec addition | Frontend form blocked | Author + reviewer + HR/IT |
| **CR-03** | API01 idempotency key — contract addition, tied to Q03 | Duplicate requests possible | Author + reviewer + PM |
| OD-10 | Client for manager/HR/downstream actions, and downstream stakeholders' visibility of the request (API02 excludes them) | API03/API04 need a caller; downstream users act blind | PM + TL |

### Editorial carry-over

The approved spec's Context section still says "11 open items (Q01–Q11)" (Gate 1 Part 5,
non-blocking). Fold into the next spec revision — likely the one CR-01/CR-02/CR-03 trigger.

## Constitution Check

- [x] No new datastore, queue, or service introduced — none proposed without an ADR (ADR-0003
      sync option and ADR-0005 async option would need one).
- [x] Layering respected — rules/authorization in Laravel, never Next.js; Policies, Form Requests,
      Resources per `int-standards.laravel.md`.
- [x] Testing discipline — test-first, 80% changed-file floor; frameworks deferred to ADR-0002.
- [x] Security posture — auth on all endpoints; no PII in logs; secrets via env; secure errors.
- [x] Rate-limit decision made explicit for every endpoint — proposed values, pending approval.
- [x] Non-functional baselines addressed — p95 < 400 ms, 99.9% availability.
- [ ] **Architecture/security sign-off** — required by Blueprint §12.1 because this plan touches
      Security Posture and Architectural Constraints; assigned to Subhajit Mukherjee, pending.

## Explicitly Deferred

- System integrations with Payroll/IT/Facilities/HRIS — manual via API04 only (ADR-0005).
- Notifications, reminders, SLA escalation — Q04/OD-09.
- Withdrawal/cancellation — Q09, out of scope.
- Stakeholder work queues/dashboards — out of scope per spec.
- API01 idempotency key — CR-03.
- Exposing the transition log — spec says audit isn't required.

## Sequencing (high level — Day 6 turns this into tasks)

1. Scaffold Laravel + Next.js and record ADR-0002 in both int-standards files.
2. Decide ADR-0001 (auth) and ADR-0004 (roles); stub both behind interfaces so work isn't blocked.
3. Persistence + domain state machine (§F, §G) with its unit tests.
4. API01 → API02 → API03 → API04, each test-first against UT/STATE/AUTH cases (after CR-01).
5. Error envelope, correlation id, rate limits, logging (§M).
6. Next.js: status view, then form (form blocked on CR-02/ADR-0003).
7. Contract tests, accessibility, cross-browser.

## Review checklist (before Tasks)

- [x] Technical reviewer assigned (R-01) — Subhajit Mukherjee, 2026-10-01
- [ ] ADR-0001, 0002, 0004 decided (block the first tasks)
- [ ] CR-01 agreed and the three test cases updated by their owner
- [ ] CR-02 decided or the form explicitly deferred
- [ ] Rate-limit values approved (ADR-0010)
- [ ] Every open decision above still explicit — none silently resolved

---

# Gate 2 Technical Review — Plan (Blueprint §13, "Gate 1 continued", with §12.1 sign-off)

| | |
|---|---|
| **Reviewer** | Subhajit Mukherjee (Gate 2 Reviewer) — distinct from author and Gate 1 reviewer |
| **Date** | 2026-10-01 |
| **Reviewed** | This plan, read against the Approved spec v1.2.1, BRD-001 (incl. controlled assumptions), `constitution.md`, the v1.2.1 test cases, and the Blueprint security checklist (§20/§30) |
| **Scope** | Plan only. No code, tasks or ADR files exist, so there is no diff to review; per-task Gate 2 starts Days 7–8 |
| **Decision** | **Changes Requested** (binary per §12.3). The approach is sound; five items below must be fixed or answered before I sign off and before Day 6 derives tasks |
| **Architecture/security sign-off (§12.1)** | **Withheld** — open until B1–B5 are closed. The Constitution Check's last box stays unticked |

## What I checked and accept

- Layering and the Laravel/Next.js boundary match the constitution; no business rule or
  authorization decision is placed in the frontend.
- Labelling every statement Fact / Approved / Rule / Proposed / Open is the right call with no
  scaffold in the repo. Nothing in the plan silently resolves Q06/Q11/Q12/Q13; Q03 and OD-03 stay open.
- Never persisting `hr_approved`, deriving `pendingWith`/`stages[]` on read, and one transaction per
  state change with a request-row lock are sound and testable (ADR-0006, ADR-0007).
- Using opaque, non-sequential ids and keeping reference/org data out of this feature's ownership
  is correct.
- Rate limits are stated per endpoint (constitution rule satisfied in form; amendments in N3).

## Blocking findings — must be resolved before sign-off

| ID | Plan § | Finding | Required change |
|---|---|---|---|
| **B1** | H.3, CR-01, header ("does not change any AC, API contract…") | I accept the proposed API03 order, in particular rule 5 (a caller who already decided their own stage gets `409 already_processed`). It is what makes a double-click (`CONC-02`) return 409 rather than a confusing 403. But it **changes the meaning of the approved API03 exception table**: the contract says `403` for "not the stakeholder the currently-pending stage requires", and a manager on a `manager_approved` request is exactly that, yet the plan returns 409. `already_processed` is also defined in the spec as a *concurrent race*, and rule 5 uses it for a sequential repeat. This is a **spec amendment**, not just a test-case edit, and the plan's claim that it changes no API contract is not accurate | (1) Reword the header/Derived From claim. (2) Raise CR-01 as a spec change (v1.2.2) to Sourav Kumar Maity: amend the API03 exceptions table and the `already_processed` definition to state the precedence order. (3) Update `AUTH-07`, `STATE-06`, `STATE-08` and confirm `CONC-02`. (4) Each precedence rule 1–8 gets its own failing test before API03 is implemented |
| **B2** | E, H.3 (API04 order) | API04 checks `404` (request or step missing) **before** the role check `403`. The spec says the 404 must not "reveal which steps apply to a request the caller isn't authorized to act on". With this order, any authenticated user can tell, per request id, whether a step exists (404 vs 403) | Check the caller's role for `{step}` first (it does not depend on the request), then request/step existence: `401` → `403` (no role for `{step}`) → `404` → `409`. Add a test for "role-less caller, unknown request" and "role-less caller, real request" returning the same `403` |
| **B3** | J, K, M, Constitution Check | The plan says "no outbound calls" (J, K, M "Integration logging: not applicable") and ticks "no new … external service", while H.1, I and OD-05/06 propose **live reads** from the portal IdP/HRIS for authentication, manager relationship, reference data and the employee's current dept/loc/role. These are outbound dependencies on the request path of every endpoint. Failure handling (timeout, source down, stale cache), the error to return (the spec's envelope only has `500`), logging, and the effect on the p95 < 400 ms target are all missing | (1) Replace "no outbound calls" with the real dependency list. (2) Add K rows: source timeout/unavailable at API01 (snapshot read), API02/03/04 (relationship lookup), reference-data validation. (3) State the error mapping — either `500 internal_error` (no contract change) or a new `503` (spec amendment; bundle with B1). (4) State timeouts and cache TTL/staleness for authorization data (a stale cache must not keep a revoked HR role alive). (5) Re-word the Constitution Check box to "depends on ADR-0001/0003 approval" |
| **B4** | H.2 (new gap) | **Self-approval / segregation of duties is not addressed.** A user holding the HR role can submit a transfer, and HR (AC16) then approves it; the same applies to a downstream-role holder completing a step on their own transfer. Neither the spec nor the plan says the requester may not act on their own request | Raise as a new open decision for Gate 1 (suggest Q14) and add to the plan's Risks table. Reviewer recommendation: a Policy rule that **denies any stage action by the requester on their own request**, regardless of roles held. Do not implement until Gate 1 records the rule (it is a business/authorization rule, so it needs an AC) |
| **B5** | O, P (R-01), `constitution.md` Governance | Every ADR in §O needs approval by "TL", but the constitution says the Technical Lead is **not assigned**. I can review, but ADR-0001/0002/0003/0004 cannot be "decided" with no approver, and the constitution owner is also TBD (amendments need one) | Name an ADR approver for this feature (and who co-signs ADR-0001/0004 for Security) and record it in the constitution's role table. Until then ADR status stays Proposed and Day 6 tasks depending on them stay blocked |

## Non-blocking findings — fix in this plan revision or carry as tracked items

| ID | Plan § | Finding | Recommendation |
|---|---|---|---|
| N1 | J, G, N | Per the BRD Q07 assumption, **org-info always applies**, so HR approve always creates ≥1 step and the "zero steps → `completed`" branch (AC11 direct path, UT11) is **unreachable through the API**. Also, a request whose target equals the current dept/loc/role is a no-op transfer; the spec is silent on whether it is allowed | Say so in G and N: test the zero-step branch at domain-unit level with applicability stubbed, and do not claim it is covered end to end. Ask the author/Gate 1 whether the no-op case needs a rule (propose Q15). Do not decide it in code |
| N2 | C, F, OD-06 | Snapshotting the manager at submission strands the request if the manager leaves or moves: AC15 says "the employee's manager", and there is no cancel (Q09), reassign, delegation (Q11) or escalation (Q08) | My recommendation for OD-06: resolve the manager **at action time** from the ADR-0004 source and keep the submission-time value only as informational. If you keep the snapshot, add "stuck request, no recovery path" to Risks |
| N3 | M, ADR-0010 | Per-user throttling does nothing for unauthenticated floods (401s). Laravel's limiter needs a cache store, and Redis would be a **new datastore** (constitution). API01 at 10/min allows ten duplicates a minute while Q03/CR-03 leave API01 unprotected | Approve the values with these changes: API01 → **5/min** as an interim duplicate mitigation; add an IP-keyed limit for unauthenticated attempts; return `Retry-After`; state the limiter's cache store and that it uses an already-approved store. Record the decision in ADR-0010 |
| N4 | M, ADR-0009 | Accepting an inbound `X-Request-Id` lets a client inject arbitrary text into every log line (log injection/spoofing) | Accept only a bounded, validated format (e.g. length ≤ 64, `[A-Za-z0-9-]`), otherwise generate one. Add a test |
| N5 | F, ADR-0008 | The spec says audit logging is **not** a requirement and tells us to amend the constitution first if it is. `TransitionLog` plus `decided-by`/`completed-by` refs is a persisted record of who approved what — an audit trail in all but name — with no retention or access rule | Accept ADR-0008 only if it states: purpose (operational integrity, not audit), retention period, who may read it, no exposure via API, ids only. If the business wants it as audit, that is a constitution amendment first |
| N6 | F, M | Data-in-transit and at-rest handling are not stated (Blueprint security checklist). `reason` is rated High sensitivity (free text), and HR/manager visibility of `reason` is a known spec gap | Add: TLS-only, at-rest encryption expectation for the DB, retention for `reason`, and a request to Gate 1 on who may see `reason` |
| N7 | L, ADR-0006 | `SELECT … FOR UPDATE` is ignored on SQLite (Laravel's default test DB), so `CONC-01/02` and the last-two-steps race would pass without testing anything. Guard evaluation also sits before the lock in H.3 (read, then re-check at rule 8) | ADR-0002 must pick an engine that supports row locks **and** CI tests must run on that engine. Lock first, then evaluate rules 3–6 under the lock. Add DB unique constraints on (request, stage) and (request, step) as a backstop |
| N8 | Sequencing 1 | Scaffolding is not behaviour but is not in the constitution's trivial tier either, and test-first has "no exceptions" | Day 6 must state how the scaffold task satisfies test-first (e.g. a failing health/smoke test written first) or record a reviewer-approved exception on the task |
| N9 | Sequencing, M | No CI step for SAST/DAST/dependency scanning (Blueprint §20, security checklist item) | Add a pipeline task in Sequencing, early (right after the scaffold), so every later task is scanned |
| N10 | F (derivation) | `pendingWith` = first open step in the fixed order org_info→payroll→it→facilities. Acceptable as the P4-09 resolution, but the steps are parallel and the order reads as a dependency | Write it into the spec in the same v1.2.2 revision as B1 so the frontend and tests rely on a contract, not on the plan; frontend renders `stages[].steps[]` |
| N11 | H.1, ADR-0001 | If option (b) cookie auth is chosen: CSRF protection, CORS, SameSite and session-fixation handling are not in the ADR's scope | Add them to ADR-0001's required content; Security co-sign required either way |
| N12 | H.2, OD-10 | Downstream stakeholders can complete a step (API04) but cannot view the request (API02), so they act blind | Agree this should go to Gate 1 as a spec decision with CR-02 rather than being fixed in code |

## Disposition of ADR candidates (reviewer position — no ADR file may be written until B5 is closed)

| ADR | Reviewer position |
|---|---|
| 0001 Authentication | **Not decided.** Prefer reusing the existing portal IdP (a) if it exists and is reachable; needs facts from the portal owner. Must cover N11. Security co-sign |
| 0002 Stack baseline | **Not decided.** Needs an owner (B5). Constraint from this review: DB engine supports row locks and is the CI test engine (N7) |
| 0003 Reference/org data | **Not decided.** Reject option (c), local system-of-record tables. Prefer (a) live read with bounded cache over (b), which adds a scheduler. Must cover B3 |
| 0004 Role mapping | **Not decided.** Depends on Q11/Q06. Must include the self-action rule once Gate 1 rules on B4 |
| 0005 Integration pattern | **Accept** manual-only for v1; business owner to confirm |
| 0006 Concurrency | **Accept** pessimistic row lock, with N7 |
| 0007 Persisted state model | **Accept** |
| 0008 Transition log | **Conditional accept** — N5 |
| 0009 Correlation id | **Accept** with N4 |
| 0010 Rate limits | **Accept** with N3 amendments |
| 0011 API01 idempotency | **Accept** UI-only as interim, with the tighter API01 limit; CR-03 and Q03 stay open |

## Change requests

- **CR-01:** agreed in substance (B1). Needs Sourav Kumar Maity's Gate 1 re-review as spec v1.2.2.
- **CR-02:** agreed it is a spec addition. Decide by Day 6 whether the form is built against a stub endpoint or explicitly deferred; the form task must not start on an invented endpoint.
- **CR-03:** deferred with Q03; no change in this review.
- **New:** Q14 (self-action, B4) and Q15 (no-op transfer, N1) to be raised by the author.

## Requirements for Day 6 `tasks.md` (so the next review is quick)

1. One task per reviewable PR, each naming its ACs/APIs/UTs by ID; no task spans two endpoints.
2. Each task records where the **Red** evidence is captured (failing test output or commit), per the constitution; I verify it at Gate 2.
3. First tasks: scaffold (N8), CI scanning (N9), then the domain state machine with unit tests.
4. Tasks that depend on an undecided ADR or CR are marked blocked by ID, not started against a guess.
5. Logging tasks carry an explicit "no PII" test (ids only; `reason` and target values never logged).

## Re-review checklist (what I will check when this is resubmitted)

- [ ] B1: spec v1.2.2 amendment submitted to Gate 1; plan header claim corrected; test cases updated
- [ ] B2: API04 order changed to role → existence → state, with the two paired tests
- [ ] B3: dependency list, failure rows, error mapping, cache/staleness rules; Constitution Check re-worded
- [ ] B4: Q14 raised to Gate 1 and listed in Risks
- [ ] B5: ADR approver named in the constitution
- [ ] N1–N12 each either fixed or listed as a tracked item with an owner
