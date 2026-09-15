# Spec: Employee Internal Transfer Digital Journey

## Spec ID
`emp-internal-transfer`

## Status
`Gate 1 — Pass with Conditions (revision required before re-review)`

## Gate 1 Review
**Author:** Aditya Hazra (SDD Developer) · **Gate 1 Reviewer:** Sourav Kumar Maity · **Review Date:** 2026-09-15

The reviewer is not the author. This spec is not Approved until the reviewer above records a
Gate 1 decision (Approved / Changes Requested) against the acceptance criteria, API contract, and
open decisions in this document.

**Recorded decision: `PASS WITH CONDITIONS`.** This is the reviewer's own wording, not one of the
two decision states this section otherwise defines. It is recorded verbatim below rather than
silently mapped onto "Approved" or "Changes Requested" — per this project's discovery rule, an
ambiguous input is recorded as-is, not resolved on its behalf. For gating purposes, since
conditions remain outstanding, this spec is **not yet Approved**: `plans/`/`tasks/` remain blocked
(per `status.md`'s Baseline artefacts table) until a revised spec addressing the conditions below
goes through another Gate 1 pass. The reviewer and/or developer should confirm whether "Pass with
Conditions" should be formalised as a third decision state for this project, or whether future
reviews should pick between the existing two.

### Gate 1 Review Comments — Sourav Kumar Maity, 2026-09-15

Recorded as provided by the reviewer, reviewing `BRD.md#BRD-001` and this spec together.

**Overall assessment:** The BRD/spec demonstrate a good understanding of the Employee Internal
Transfer journey and follow the SDD discovery approach well — business objectives, actors, journey
stages, assumptions, dependencies, open questions, and the business/technical decision split are
all appropriately identified. The identification of ambiguities is particularly good; several
unspecified areas are correctly left open rather than silently converted into confirmed business
rules.

**Key strengths noted:**
- Good understanding of the business journey — employee/manager/HR/downstream-stakeholder
  responsibilities and the major journey stages are captured correctly; employee-facing
  capabilities align with the source requirement.
- Good ambiguity and discovery analysis — missing business decisions are identified rather than
  assumed; questions around eligibility, effective date, concurrent requests, downstream
  activities, rejection, RBAC, and integrations are appropriately raised.
- Clear business vs. technical distinction — API, data model, orchestration mechanism, and
  technology choices are correctly treated as technical concerns.
- Good traceability foundation — requirements trace back to the source assessment document,
  giving a reasonable base for Spec → Acceptance Criteria → API Contract → Test Cases → Technical
  Plan.

**Observations / required clarifications before the spec is fully ready for the next SDD stage:**

1. **Conditional downstream activities — Critical.** The requirement states Payroll, IT, and
   Facilities activities apply "only where required," without a business rule for *when* each
   applies (when is Payroll involvement required; when is IT provisioning/removal required; when
   is Facilities involvement required). The specification must not invent these rules without
   business confirmation. *(Maps to the existing open item [Q07](../BRD.md); AC10/AC11.)*
2. **Rejection and failure handling — Critical.** Define the expected business behaviour for:
   manager rejection, HR rejection, a failed Payroll update, a failed IT provisioning/removal, and
   a Facilities activity that cannot be completed — and whether a downstream failure results in a
   failed request, a pending/manual-intervention state, a retry, an escalation, or another
   business-defined outcome. Resulting state transitions must be based on confirmed business
   decisions, not technical assumptions. *(Maps to the existing open item [Q08](../BRD.md);
   AC07/AC09.)*
3. **Manager → HR sequencing.** The BRD/spec interpret the journey as Employee → Manager → HR →
   Downstream activities. Confirm explicitly whether HR validation must happen only after manager
   approval, or whether Manager and HR activities can happen in parallel — the source presents
   them sequentially, but this should be treated as a business rule needing confirmation if it
   affects the state machine. *(Not fully covered by an existing Q-item — BRD.md's "Business
   decisions" section currently states sequential order as decided from the source's step
   ordering, line 104-105; the reviewer's point is that "sequential in the source's list" and
   "sequential as an enforced state-machine rule, with parallel explicitly ruled out" are not
   necessarily the same confirmed decision. Flagged for the author to consider as a BRD
   clarification/new open item.)*
4. **Stakeholder ownership.** The organisational-information update is a journey stage but its
   responsible stakeholder/team is not clearly identified: who owns it, is it HR or another team,
   and should it appear as a separate stakeholder/action in the employee's "pending with" view?
   *(Maps to the existing open item [Q06](../BRD.md); AC10.)*
5. **Authorization / RBAC.** Good that RBAC is already an open question ([Q11](../BRD.md)) — avoid
   treating Employee + Manager + HR as a confirmed access model unless explicitly approved (see
   `BRD.md`'s Authorization/RBAC section, which currently frames it as a working assumption
   pending confirmation, not a decision). Clarify who can view the request, approve/reject it,
   perform downstream actions, and view requests across employees (if applicable).
6. **Department / Location / Role data.** The employee selects department/business unit, location,
   and role/job position — identify the business rule/source determining which values are
   valid/selectable. Technical implementation of the source can be decided later, but the business
   definition of valid options should be clear before finalising the specification. *(Not covered
   by an existing Q-item; overlaps this spec's "Contract Gaps" reference-data-endpoint gap, but
   that gap is framed as a technical/API question — this observation is about the underlying
   business rule for valid options, which is a distinct, currently untracked gap. Flagged for the
   author to consider as a new BRD open item.)*
7. **Employee confirmation.** Clarify what "employee receives confirmation" means in the final
   journey — immediately after submission, after manager approval, after HR approval, or only
   after all applicable downstream activities complete. The final specification should
   distinguish submission confirmation from transfer-completion confirmation, if both are
   required. *(Partially covered: AC01 already asserts a submission-time status view and AC11 a
   completion-time confirmation, but the spec does not yet explicitly frame these as two distinct,
   named confirmation events. Flagged for the author to consider as a BRD/spec clarification.)*

**Recommendation for next revision:** focus particularly on (1) conditional downstream workflow,
(2) rejection/failure handling, (3) manager/HR sequencing, and (4) authorization and stakeholder
ownership. Where a business decision is not available, retain it explicitly as an Open
Decision/Assumption rather than creating an implicit rule. Once addressed, the specification can
proceed to the next SDD stage with a stronger foundation for Spec → Acceptance Criteria → API
Contract → Test Cases → Technical Plan.

**Gate 1 decision (reviewer's words):** PASS WITH CONDITIONS — "The submission demonstrates good
SDD discovery capability and a strong understanding of the business journey. Approval is
conditional on addressing the above workflow and authorization ambiguities before proceeding to
detailed implementation planning."

## Linked BRD
[`.ai-context/BRD.md#brd-001-employee-internal-transfer-digital-journey`](../BRD.md#brd-001-employee-internal-transfer-digital-journey)

## Intent

This feature gives an employee of the organisation a single digital journey inside the One-Point
Employee Portal to request, track, and see through to completion an internal transfer to a
different department/business unit, location, and/or role, replacing today's manual coordination
across their manager, HR, and — where applicable — the organisational-information, Payroll, IT,
and Facilities functions. An authenticated employee initiates a request by specifying the proposed
department/business unit, location, and role, an effective date, and an optional reason; from
submission onward, the employee has one place to see the request's current status and which
stakeholder — their manager, HR, or a downstream function — an action is currently pending with,
until the request reaches a final outcome. The Laravel API is the sole authority for whether a
request is valid, who may act on it, what state it is in, and when it transitions; the Next.js
frontend is responsible only for presenting the request form, the status/timeline view, and any
errors or pending-action information the API returns — it makes no eligibility, authorization, or
state decisions of its own.

## Context

- [`BRD.md#BRD-001`](../BRD.md) — the source requirement this spec is derived from, including the
  11 open items (Q01–Q11) referenced throughout this spec.
- [`constitution.md`](../constitution.md) — non-negotiables this spec must not violate, in
  particular the Architectural Constraints (backend/frontend boundary) and Security Posture
  sections.
- [`project_context.md`](../project_context.md) — confirms this repository has no existing
  Laravel or Next.js source tree yet; this is the **first** feature specified for the One-Point
  Employee Portal, so there are no existing portal capabilities, auth/RBAC modules, or frontend
  modules to link to or reuse.
- `architecture.md` — **does not exist.** It documented the architecture of an unrelated prior
  project ("Empty Floor + Circle Tap") that occupied this repository before it was repurposed, and
  was removed on 2026-09-04 once confirmed unneeded (see `project_context.md`'s "Prior project
  cleanup" note). No architecture document exists yet for the One-Point Employee Portal; one is
  expected to emerge from the Day 5 technical plan.
- [`.agent/rules/int-standards.laravel.md`](../../.agent/rules/int-standards.laravel.md) and
  [`.agent/rules/int-standards.nextjs.md`](../../.agent/rules/int-standards.nextjs.md) — record
  that auth mechanism, API versioning, routing model, and test frameworks are all `[Open]` Day 5
  decisions. This spec does not assume any of them.

## API Contract

This contract is framework-neutral: it describes the product-level request/response shape the
Laravel API must honour and the Next.js frontend may rely on. No controller, Eloquent model,
migration, middleware class, or other Laravel-specific implementation detail appears here — those
belong to the Day 5 technical plan.

> **Base path is an open specification decision, not a repository fact.** No API base
> path/versioning convention exists anywhere in this repository yet (`constitution.md` marks API
> versioning `[Open]`; no Laravel scaffold exists to inspect). The paths below use
> `/api/v1/transfer-requests` as a **proposed** convention, flagged here for Gate 1 to confirm,
> amend, or replace — implementation must not treat it as already decided.
>
> **Auth mechanism is likewise `[Open]`** (Sanctum / Passport / session guards — see
> `.agent/rules/int-standards.laravel.md`). Every endpoint below states *that* authentication is
> required and *what* authorization relationship it checks; it does not name a mechanism.

### Status and pending-with vocabulary (proposed, for Gate 1 confirmation)

These values are used consistently across all three endpoints. They are a specification-level
proposal derived from the ACs' transitions, not a confirmed BRD decision — the downstream-step
names in particular depend on Q06/Q07 (open).

| `status` | Meaning | Terminal? |
|---|---|---|
| `submitted` | AC01 — request created, awaiting manager | No |
| `manager_approved` | AC06 — manager approved, awaiting HR | No |
| `manager_declined` | AC07 — manager declined | Yes |
| `hr_approved` | AC08 — HR confirmed eligibility; downstream processing begins if applicable | No |
| `hr_declined` | AC09 — HR did not confirm eligibility | Yes |
| `downstream_processing` | AC10 — one or more of org-info/payroll/IT/facilities pending | No |
| `completed` | AC11 — all applicable downstream steps complete | Yes |

| `pendingWith` | Meaning |
|---|---|
| `manager` | AC06/AC15 |
| `hr` | AC08/AC16 |
| `org_info` \| `payroll` \| `it` \| `facilities` | AC10 — **illustrative only**; which of these apply to a given request, and who owns `org_info`, are Q06/Q07 (open) |
| `none` | Terminal states (`manager_declined`, `hr_declined`, `completed`) |

### `emp-internal-transfer.API01` — Create Transfer Request

**Business action:** AC01, AC02, AC03, AC04

`POST /api/v1/transfer-requests` *(proposed path — see note above)*

- **Auth:** Required. Any authenticated employee may create a request for themselves; this
  endpoint does not accept a request on behalf of another employee.
- **Headers:** `Content-Type: application/json`; `Authorization` per the mechanism Day 5 selects.
- **Request payload:**

  ```json
  {
    "targetDepartmentId": "string, required",
    "targetLocationId": "string, required",
    "targetRoleId": "string, required",
    "effectiveDate": "string, required — ISO 8601 date (YYYY-MM-DD)",
    "reason": "string, optional, nullable"
  }
  ```

  | Field | Type | Required | Validation |
  |---|---|---|---|
  | `targetDepartmentId` | string (reference id) | Yes | Must reference an existing, currently selectable department/business unit (AC04) |
  | `targetLocationId` | string (reference id) | Yes | Must reference an existing, currently selectable location (AC04) |
  | `targetRoleId` | string (reference id) | Yes | Must reference an existing, currently selectable role/job position (AC04) |
  | `effectiveDate` | ISO 8601 date | Yes | Must be a structurally valid date (AC03). **No minimum-lead-time rule is enforced — Q02 is open; do not implement a "must be N days out" check against this contract.** |
  | `reason` | string | No | No format constraint stated by the BRD |

  The reference-data source that validates `targetDepartmentId`/`targetLocationId`/`targetRoleId`
  is a **contract gap**, not defined by this spec — see "Contract Gaps" below.

- **Success — `201 Created`:**

  ```json
  {
    "data": {
      "transferRequestId": "string",
      "status": "submitted",
      "pendingWith": "manager",
      "targetDepartmentId": "string",
      "targetLocationId": "string",
      "targetRoleId": "string",
      "effectiveDate": "2026-01-01",
      "reason": "string | null",
      "submittedAt": "ISO 8601 timestamp"
    }
  }
  ```

- **Exceptions:**

  | HTTP | `errorCode` | Condition | Safe response behaviour |
  |---|---|---|---|
  | 400 | `validation_error` | A required field is missing (AC03) | Field-level `errors[]` names each missing field; no partial record created |
  | 400 | `invalid_reference_data` | `targetDepartmentId`/`targetLocationId`/`targetRoleId` does not reference a currently selectable option (AC04) | `errors[]` names the invalid field; no record created |
  | 400 | `malformed_payload` | Body is not valid JSON, or `effectiveDate` is not a structurally valid date | Generic safe message; no record created |
  | 401 | `unauthenticated` | No valid credentials presented | No information about any transfer request is revealed |
  | 429 | `rate_limited` | *(shape only — no threshold decided yet; constitution requires an explicit per-endpoint rate-limit decision at the technical-plan stage)* | Standard retry-after semantics once a limit is set |
  | 500 | `internal_error` | Unexpected server failure | Generic message; no stack trace, SQL, or infrastructure detail |

  **Deliberately absent:** a duplicate/concurrent-active-transfer exception (e.g. `409
  duplicate_active_request`). The BRD does not confirm this rule (Q03) — see the spec's "Duplicate
  / concurrent active-transfer protection" section. Do not add this response without a spec
  revision resolving Q03 first.

### `emp-internal-transfer.API02` — Get Transfer Details

**Business action:** AC12, AC13, AC14, AC17

`GET /api/v1/transfer-requests/{transferRequestId}`

- **Auth:** Required. **Authorization:** the caller must be the request's own employee, the
  employee's manager (AC15's relationship), or a user holding the HR role (AC16) — per AC14/AC17,
  no other relationship grants access.
- **Success — `200 OK`:**

  ```json
  {
    "data": {
      "transferRequestId": "string",
      "status": "submitted | manager_approved | manager_declined | hr_approved | hr_declined | downstream_processing | completed",
      "pendingWith": "manager | hr | org_info | payroll | it | facilities | none",
      "targetDepartmentId": "string",
      "targetLocationId": "string",
      "targetRoleId": "string",
      "effectiveDate": "2026-01-01",
      "reason": "string | null",
      "stages": [
        { "stage": "manager_confirmation", "status": "pending | approved | declined" },
        { "stage": "hr_eligibility", "status": "pending | approved | declined | not_applicable" },
        { "stage": "downstream_processing", "status": "pending | in_progress | complete | not_applicable",
          "steps": [ { "step": "org_info | payroll | it | facilities", "status": "pending | complete" } ] }
      ]
    }
  }
  ```

  The `stages`/`steps` shape gives AC10's "each applicable step tracked individually" and AC12/13's
  status + pending-action visibility. It intentionally carries no actor identity or decision
  timestamp per stage — see "Auditability" in the spec: that level of detail is not a confirmed
  requirement. `pendingWith` and per-step visibility are returned **at the level the requesting
  user is authorized to see**; this spec does not define a reduced view for any of the three
  permitted viewers (employee/manager/HR) beyond the isolation AC14/AC17 already require — the
  spec is silent on whether e.g. a manager should see the `reason` text, and this is a contract gap.

- **Exceptions:**

  | HTTP | `errorCode` | Condition | Safe response behaviour |
  |---|---|---|---|
  | 401 | `unauthenticated` | No valid credentials presented | No information revealed |
  | 403 | `forbidden` | Authenticated, but caller has no permitted relationship to this request (AC14, AC17) — **this spec's decision:** the request's existence is not treated as sensitive, so a real ID with no relationship returns 403, not a disguised 404 | Generic message; no request data revealed |
  | 404 | `not_found` | No transfer request exists with the given ID | Identical response whether the ID is malformed or simply unassigned — no distinction that would help enumerate valid IDs |
  | 429 | `rate_limited` | *(shape only — no threshold decided yet)* | As API01 |
  | 500 | `internal_error` | Unexpected server failure | Generic message only |

  > The 403-vs-404 split above is a specification decision this spec is making (an API-shape
  > question, not a business rule), flagged for explicit Gate 1 confirmation alongside the base
  > path — no existing repository security convention exists to defer to.

### `emp-internal-transfer.API03` — Approval / Stage Action

**Business action:** AC06, AC07, AC08, AC09, AC15, AC16, AC17

`POST /api/v1/transfer-requests/{transferRequestId}/actions`

- **Auth:** Required. **Authorization:** the server determines which stage is currently pending
  and accepts the action only from the stakeholder that stage requires — the employee's manager
  when `pendingWith: manager` (AC15), a user with the HR role when `pendingWith: hr` (AC16). The
  request body does not name a stage; the server infers it from current state, so a caller cannot
  assert a stage they aren't authorized for.
- **Request payload:**

  ```json
  { "decision": "approve | decline" }
  ```

  | Field | Type | Required | Validation |
  |---|---|---|---|
  | `decision` | enum: `approve`, `decline` | Yes | No other value accepted |

  For the HR stage, `approve` corresponds to AC08 ("HR confirms eligibility") and `decline` to AC09
  ("HR does not confirm eligibility") — this contract reuses one vocabulary across both stages
  rather than inventing stage-specific verbs, since the BRD does not require different ones.

- **Success — `200 OK`:** same `data` shape as API02, reflecting the new `status`, `pendingWith`,
  and `stages`.
- **Exceptions:**

  | HTTP | `errorCode` | Condition | Safe response behaviour |
  |---|---|---|---|
  | 400 | `validation_error` | `decision` missing or not one of the allowed values | Field-level `errors[]`; no state change |
  | 401 | `unauthenticated` | No valid credentials presented | No information revealed |
  | 403 | `forbidden_wrong_stakeholder` | Authenticated, but caller is not the stakeholder the currently-pending stage requires (AC15, AC16, AC17) | Generic message; no state change |
  | 404 | `not_found` | No transfer request exists with the given ID | Same behaviour as API02 |
  | 409 | `invalid_state_transition` | The request is in a terminal state (`manager_declined`, `hr_declined`, `completed`) or otherwise not currently awaiting any human decision | No state change; message indicates the request is not actionable, without detailing why in a way that leaks other stakeholders' data |
  | 409 | `already_processed` | The specific pending action this caller would perform was already decided by the time this request was received (concurrent decision race) | No state change; message indicates the action was already taken |
  | 429 | `rate_limited` | *(shape only — no threshold decided yet)* | As API01 |
  | 500 | `internal_error` | Unexpected server failure | Generic message only |

  > `invalid_state_transition` and `already_processed` are deliberately distinct: the first covers
  > acting on a request that isn't awaiting a decision at all; the second covers two legitimate
  > actors racing for the same currently-pending decision. Both are shape-only contract
  > definitions — neither depends on an unconfirmed business rule.

### Contract Gaps / Open Decisions (not invented as endpoints here)

| Gap | Why it's not defined above |
|---|---|
| Reference-data endpoints for selectable departments/locations/roles (to populate the request form and back AC04's validation) | Not named by the BRD or any existing module (this is the first feature in this repository) — flagged for Gate 1 / Day 5, not invented here |
| A dedicated downstream-status detail endpoint beyond the `stages`/`steps` shape in API02 | Depends on Q06 (org-info stage ownership) and Q07 (applicability rule) — both open |
| Any endpoint for the manager/HR/Payroll/IT/Facilities side to *act outside* API03's single decision shape (e.g. a bulk queue view for HR) | Out of scope per the spec's "Explicitly Out of Scope" — no dedicated stakeholder interfaces are defined |
| Withdrawal/cancellation endpoint | Q09 is open and withdrawal is explicitly Out of Scope |

### Error Contract

All error responses across every endpoint in this feature use one envelope:

```json
{
  "error": {
    "errorCode": "string — stable, machine-readable, snake_case",
    "message": "string — human-readable, safe to display, no internals",
    "errors": [ { "field": "string", "message": "string" } ],
    "correlationId": "string — present if the repository's request-tracing convention supports it"
  }
}
```

- `errors[]` is present only for `validation_error`/`invalid_reference_data` responses; absent
  otherwise.
- `correlationId` is included **if** the eventual Laravel implementation has a request-correlation
  convention; none exists yet to verify, so its presence is conditional, not guaranteed by this
  spec.
- No response body, under any `errorCode`, may contain: PII beyond what the endpoint's own success
  response already authorizes the caller to see, credentials, stack traces, SQL, file paths, or
  infrastructure details — per `constitution.md`'s Security Posture and both stack rule files.
- This envelope shape is itself a **proposed specification decision** — `.agent/rules/int-standards.laravel.md`
  notes the repository has no established error envelope yet. Flagged for Gate 1.

### Next.js Consumption Contract

What the frontend can rely on, without reimplementing any authoritative rule itself:

- **Success payload shapes** are exactly the `data` objects defined above for API01/API02/API03 —
  the frontend renders them, it does not recompute `status`, `pendingWith`, or `stages`.
- **`status` and `pendingWith` are a closed, stable enumeration** (see the vocabulary table above).
  The frontend may switch UI on these string values but must treat an unrecognised value as an
  unexpected/error display state, not silently ignore it.
- **Validation errors** (`validation_error`, `invalid_reference_data`) arrive with a field-level
  `errors[]` the form binds directly to; the frontend does not duplicate field-validity rules
  beyond basic input ergonomics (e.g. a date picker), since `.agent/rules/int-standards.nextjs.md`
  requires the API remain the enforcement point.
- **Authorization errors** (`401`, `403`) are handled generically (redirect to sign-in for 401;
  an authorization-appropriate message for 403) — the frontend does not infer *why* it was denied
  beyond what `errorCode` states, and per this spec's Frontend/Backend boundary, the UI should
  avoid offering an action (e.g. an approve/decline button) it already knows the current user
  cannot take, using `pendingWith` and the viewer's own known role/relationship — but this is a UX
  courtesy, not the enforcement point; the API still rejects it independently either way.
- **Loading/pending/error semantics:** every one of the three endpoints needs an explicit loading
  state, a distinct error-state presentation per `errorCode` family (validation vs. auth vs.
  not-found vs. conflict vs. server error), and — for API02 — an empty/no-timeline-yet state
  immediately after AC01's submission, before any stage has moved.
- **Timeline/pending-action fields** the frontend consumes are exactly `status`, `pendingWith`, and
  `stages[]` from API02/API03 — no separate audit/history endpoint is defined (see Contract Gaps).

## Acceptance Criteria

IDs are stable and prefixed `emp-internal-transfer.`. Each AC traces to BRD-001 — see
[Traceability](#traceability).

### Request initiation

**AC01 — Submit a request with all required fields**
```gherkin
Given an authenticated employee who is using the One-Point Employee Portal
When the employee submits an internal transfer request with a proposed department/business unit,
  a proposed location, a proposed role/job position, and an effective date
Then the system creates a transfer request in its initial submitted state
And the employee can see the request's current status
And the employee can see that the next action is pending with their manager
```

**AC02 — Reason is optional**
```gherkin
Given an authenticated employee has provided a proposed department/business unit, location,
  role/job position, and effective date
When the employee submits the request without providing a reason
Then the system accepts the request
And the request is created without requiring a reason value
```

### Validation and rejection of invalid or incomplete data

**AC03 — Reject a request missing a required field**
```gherkin
Given an authenticated employee is preparing a transfer request
When the employee attempts to submit the request without a proposed department/business unit,
  a proposed location, a proposed role/job position, or an effective date
Then the system rejects the submission
And the employee is told which required field(s) are missing
And no transfer request record is created
```

**AC04 — Reject a request with an unselectable department, location, or role**
```gherkin
Given an authenticated employee is preparing a transfer request
When the employee submits a proposed department/business unit, location, or role/job position
  that is not a valid, currently selectable option
Then the system rejects the submission
And the employee is told which field's value is invalid
And no transfer request record is created
```

> Field-level rules beyond "required" and "must be a valid selectable option" — such as a minimum
> lead time before the effective date — are not confirmed by the BRD. See **Open Decisions**
> below (Q02); no AC asserts a specific lead-time rule.

### Eligibility rules

**AC05 — HR records an eligibility decision on a manager-approved request**
```gherkin
Given a transfer request that has received manager approval
When HR reviews the request
Then HR can record an eligibility decision of either "eligible" or "not eligible" for the request
And the request's status reflects that decision
```

> This AC establishes only that an eligibility gate exists and produces a recorded decision. The
> **criteria** HR applies (e.g., a minimum tenure) are not confirmed anywhere in the source
> document. Per the discovery rule, no AC in this spec asserts a specific eligibility criterion —
> see **Open Decisions** below (Q01).

### Duplicate / concurrent active-transfer protection

**Not specified.** The BRD does not confirm whether an employee may have more than one active
transfer request at a time (Q03). No AC is written for this behaviour. A spec revision is required
once this is decided — until then, treat "can a second request be submitted while one is active"
as undefined, not as "no protection exists."

### Manager confirmation/approval transition

**AC06 — Manager approves**
```gherkin
Given a submitted transfer request pending action from the employee's manager
When the manager approves the request
Then the request's status transitions to reflect manager approval
And the "pending with" indicator updates to show HR as the next stakeholder
And the employee can see this transition in their status view
```

**AC07 — Manager declines**
```gherkin
Given a submitted transfer request pending action from the employee's manager
When the manager declines to approve the request
Then the request's status transitions to a non-approved state
And the employee is informed of the outcome
And no HR eligibility action becomes pending
```

> What, if anything, must be undone if a request is declined after later stages have already begun
> is covered under **Failure / rollback**, which the BRD does not resolve (Q08).

### HR eligibility validation transition

**AC08 — HR confirms eligibility**
```gherkin
Given a transfer request that has received manager approval
When HR confirms the employee is eligible
Then the request's status transitions to reflect HR approval
And the "pending with" indicator updates to show the next applicable stakeholder — a downstream
  function if one applies to this request, or completion if none does
And the employee can see this transition in their status view
```

**AC09 — HR does not confirm eligibility**
```gherkin
Given a transfer request that has received manager approval
When HR determines the employee is not eligible
Then the request's status transitions to a non-approved state
And the employee is informed of the outcome
And no downstream processing becomes pending
```

### Organisational/downstream processing and completion

**AC10 — Applicable downstream steps are tracked individually**
```gherkin
Given a transfer request that has received HR approval
And one or more of organisational-information update, payroll update, IT provisioning/removal,
  or facilities arrangement applies to this request
When the request enters downstream processing
Then the employee's status view shows each applicable step as pending until it is marked complete
```

> This AC deliberately does not state *which* steps apply to a given transfer, or the mechanism
> by which that is decided — the BRD only says these steps apply "where applicable" without
> defining the rule. See **Open Decisions** below (Q06, Q07). No system-specific downstream
> integration is asserted here; see `constitution.md`'s Architectural Constraints — no new
> integration may be introduced without an ADR regardless of what this spec eventually requires.

**AC11 — Request reaches completion**
```gherkin
Given a transfer request that has received HR approval
When every downstream step that applies to this request (if any) is marked complete
Then the request's status transitions to completed
And the employee sees confirmation that the transfer is complete
```

### Requester status and pending-action visibility

**AC12 — View current status**
```gherkin
Given an authenticated employee with a transfer request in any non-completed state
When the employee views their request
Then the employee sees the request's current status
```

**AC13 — View pending action**
```gherkin
Given an authenticated employee with a transfer request in any non-completed state
When the employee views their request
Then the employee sees which stakeholder an action is currently pending with
```

### Authorization isolation

**AC14 — Employee cannot see another employee's request**
```gherkin
Given two employees, each with their own transfer request
When one employee attempts to view or act on the other employee's request
Then the system denies the attempt
```

**AC15 — Only the relevant manager may act on the manager-confirmation step**
```gherkin
Given a transfer request pending action from the employee's manager
When a person who is not that employee's manager attempts to approve or decline the request
Then the system denies the attempt
```

**AC16 — Only a user with the HR role may act on the HR-eligibility step**
```gherkin
Given a transfer request pending HR eligibility validation
When a person without the HR role attempts to record an eligibility decision
Then the system denies the attempt
```

**AC17 — No relationship, no access**
```gherkin
Given a transfer request and a user who is neither the requester, the requester's manager, nor a
  user with the HR role
When that user attempts to view or act on the request
Then the system denies the attempt
```

> How "the employee's manager" is resolved (reporting-line source, delegation/out-of-office
> coverage) is not confirmed by the BRD (Q11). AC15 asserts the authorization **outcome**, not the
> resolution mechanism.

### Auditability

**Not specified as a requirement of this spec.** Neither the approved BRD nor the current
`constitution.md` states an audit-logging requirement for this feature (unlike the archived prior
project's constitution, which is not in force here). No AC is written asserting that state
transitions are audit-logged. If audit logging is required, it must first be added to
`constitution.md` as an approved non-negotiable, then reflected here in a spec revision.

## Open Decisions Carried Into This Spec

None of these may be silently implemented against. Each blocks the AC(s) noted until resolved —
see `BRD.md`'s Open Decisions / Assumptions / Proposed Rules table for full detail.

| Ref | Item | Affects |
|---|---|---|
| Q01 | Eligibility criteria (e.g., minimum tenure) | AC05 — gate exists, criteria undefined |
| Q02 | Minimum lead time before the effective date | No AC written — see Validation section note |
| Q03 | Duplicate/concurrent active-transfer handling | No AC written — see dedicated section above |
| Q05 | Geographic scope (domestic vs. cross-border) | Out of Scope section — cross-border excluded by default |
| Q06 | Ownership of the organisational-information update stage | AC10 — step tracked generically, owner undefined |
| Q07 | Rule deciding which downstream steps apply to a given request | AC10, AC11 — steps tracked generically, applicability rule undefined |
| Q08 | Rejection/rollback semantics once later stages have begun | AC07, AC09 — only the basic non-approved transition is asserted |
| Q09 | Whether an employee can withdraw/cancel a submitted request | No AC written — see Out of Scope |
| Q11 | RBAC resolution mechanism for "manager"/"HR" | AC15, AC16 — authorization outcome asserted, resolution mechanism undefined |

## Explicitly Out of Scope

- **Cross-border tax/legal processing.** The BRD does not confirm cross-border/relocation transfers
  are in scope for v1 (Q05); until confirmed, this spec covers domestic transfers only.
- **Compensation renegotiation.** Not mentioned anywhere in the source requirement; a transfer
  request carries a proposed department/location/role and effective date, not a compensation
  change.
- **Withdrawal/cancellation of a submitted request** by the employee. Not stated in the source
  document's employee capability list (Q09); no AC covers it.
- **Unrelated HR/payroll/IT/facilities features** — e.g., leave management, benefits enrollment,
  general IT ticketing, or facilities requests unconnected to a transfer.
- **Replacement of downstream systems.** This feature orchestrates and tracks org-info, payroll,
  IT, and facilities steps; it does not replace the systems of record for any of them.
- **New integrations not required for this journey**, and any integration at all beyond what an
  approved technical plan and ADR establish — per `constitution.md`'s Architectural Constraints.
- **Bulk/mass transfers** (e.g., team reorganisation). The source describes a single-employee-
  initiated journey only.
- **Dedicated interfaces for the manager, HR, Payroll, IT, or Facilities sides of the journey**
  beyond what AC06–AC11 require for the employee's status view to reflect their actions. This spec
  does not define those stakeholders' own working screens.
- **Any transfer type other than an internal transfer** (e.g., external hire, contractor
  conversion).

## Non-Functional Constraints

Per `constitution.md` (all `[Provisional]` unless noted):

- **p95 API latency target: < 400 ms** for endpoints introduced by this feature.
- **Availability target: 99.9%.**
- **Zero PII in logs at any level.** A transfer request necessarily carries employee-identifying
  data (department, location, role, effective date, reason); none of it may reach application logs
  unmasked, per the constitution's Security Posture.
- **Authentication and authorization required on every endpoint** this feature introduces — no
  endpoint is public. The specific auth mechanism is `[Open]` (Day 5).
- **Rate limiting** — an explicit decision is required per endpoint when this feature reaches the
  technical plan; not decided here.
- **Secrets** — none are introduced by this feature at the spec level; any that arise during
  planning must come from the repository's environment/config mechanism once established.
- **Test-first, 80% coverage floor on changed files** — applies to every endpoint and
  state-changing operation this feature introduces, once implementation begins.

## Frontend/Backend Responsibility Boundary

- **Laravel API (authoritative):** validates and accepts/rejects a submitted request; determines
  and enforces who may act on a given request and at which stage; owns the request's state and
  every transition (submitted → manager decision → HR decision → downstream processing →
  completed, or a non-approved terminal state); is the single source of truth for status and
  "pending with" data returned to the frontend.
- **Next.js frontend:** renders the request form and its validation feedback; presents the
  status/timeline view and pending-action indicator using data the API returns; presents
  authorization-aware UI (e.g., not offering an approve/decline action to a user the API would
  reject); presents loading, error, and empty states for every interaction in this journey.
- The frontend must never decide eligibility, authorization, or a state transition on its own, and
  must never be the only place such a rule is enforced — every rule the UI reflects is enforced
  independently by the API. This mirrors `constitution.md`'s Architectural Constraints and
  `.agent/rules/int-standards.nextjs.md`'s Architecture boundaries.

## Unit Test Cases (spec-derived)

One-to-one with the final ACs (17 ACs → 17 UTs). Each is independently verifiable and derived
directly from its AC's Given/When/Then — none introduces a scenario the AC doesn't already state.

| Test ID | Maps to AC | Scenario | Expected |
|---|---|---|---|
| `emp-internal-transfer.UT01` | AC01 | Authenticated employee submits with all required fields | `201`, `status: submitted`, `pendingWith: manager` |
| `emp-internal-transfer.UT02` | AC02 | Submit with every required field but no `reason` | `201`; `reason` is `null`, not defaulted |
| `emp-internal-transfer.UT03` | AC03 | Submit missing one required field (parameterised over each of the 4 required fields) | `400 validation_error`; `errors[]` names the missing field; no record created |
| `emp-internal-transfer.UT04` | AC04 | Submit with a `targetDepartmentId`/`targetLocationId`/`targetRoleId` that doesn't reference a valid selectable option (parameterised over each of the 3 fields) | `400 invalid_reference_data`; `errors[]` names the invalid field; no record created |
| `emp-internal-transfer.UT05` | AC05 | HR calls API03 on a `manager_approved` request with `decision: approve` or `decision: decline` | `200`; status becomes `hr_approved` or `hr_declined` respectively; no assertion made about *which* decision is "correct" — this UT only proves the gate produces a recorded decision |
| `emp-internal-transfer.UT06` | AC06 | Manager calls API03 on a `submitted` request with `decision: approve` | `200`; `status: manager_approved`; `pendingWith: hr` |
| `emp-internal-transfer.UT07` | AC07 | Manager calls API03 on a `submitted` request with `decision: decline` | `200`; `status: manager_declined`; `pendingWith: none`; a subsequent HR action attempt would hit `409 invalid_state_transition` (cross-check with UT16) |
| `emp-internal-transfer.UT08` | AC08 | HR calls API03 on a `manager_approved` request with `decision: approve` | `200`; `status: hr_approved`; `pendingWith` reflects the next applicable stakeholder or `none` if nothing applies |
| `emp-internal-transfer.UT09` | AC09 | HR calls API03 on a `manager_approved` request with `decision: decline` | `200`; `status: hr_declined`; `pendingWith: none` |
| `emp-internal-transfer.UT10` | AC10 | GET API02 on an `hr_approved` request with applicable downstream steps | `200`; `stages[].steps[]` lists each applicable step individually as `pending` until completed |
| `emp-internal-transfer.UT11` | AC11 | Every applicable downstream step for a request is marked complete | `status` transitions to `completed`; employee-facing response confirms completion |
| `emp-internal-transfer.UT12` | AC12 | Requesting employee calls API02 at any non-completed status | `200`; `status` field present and correct |
| `emp-internal-transfer.UT13` | AC13 | Requesting employee calls API02 at any non-completed status | `200`; `pendingWith` field present and correct |
| `emp-internal-transfer.UT14` | AC14 | Employee B calls API02 for Employee A's request | `403 forbidden` (this spec's decision — see API02 exceptions); no request data in the response |
| `emp-internal-transfer.UT15` | AC15 | A manager who is not the request's employee's manager calls API03 while `pendingWith: manager` | `403 forbidden_wrong_stakeholder`; no state change |
| `emp-internal-transfer.UT16` | AC16 | A caller without the HR role calls API03 while `pendingWith: hr` | `403 forbidden_wrong_stakeholder`; no state change |
| `emp-internal-transfer.UT17` | AC17 | A caller with no relationship to the request (not requester/manager/HR) calls API02 or API03 | `403` (API02) / `403 forbidden_wrong_stakeholder` (API03); no data or state change |

Boundary/negative coverage folded into the above rather than duplicated as separate IDs:
UT03/UT04 are parameterised boundary+invalid-input cases; UT07/UT16 double as
state-transition-rejection evidence when read together with UT08 (a subsequent HR action on an
already-`manager_declined` request — see the broader `test_cases/` file for that explicit scenario,
since it spans two UTs and doesn't map to a single AC).

## Traceability

| BRD-001 source | AC | API | UT |
|---|---|---|---|
| §3 employee capabilities + journey stage 1 | AC01 | API01 | UT01 |
| §3 "optional reason" | AC02 | API01 | UT02 |
| §3 employee capabilities (required fields); discovery rule | AC03 | API01 | UT03 |
| §3 "select" wording (selectable option set) | AC04 | API01 | UT04 |
| Journey stage 3 (HR eligibility validation); criteria = Q01 (open) | AC05 | API03 | UT05 |
| Q03 (duplicate/concurrent) — **explicitly not confirmed** | — no AC | — deliberately absent from API01 | — no UT |
| Journey stage 2 (manager confirmation/approval) | AC06, AC07 | API03 | UT06, UT07 |
| Journey stage 3 (HR eligibility validation) | AC08, AC09 | API03 | UT08, UT09 |
| Journey stages 4–7 (org-info/payroll/IT/facilities, "where applicable") | AC10 | API02 | UT10 |
| Journey stage 8 (employee confirmation/completion) | AC11 | API03 (final transition) / API02 (visibility) | UT11 |
| §3 "view current status" | AC12 | API02 | UT12 |
| §3 "view pending actions" | AC13 | API02 | UT13 |
| Actors table (§2); Authorization/RBAC questions | AC14–AC17 | API02, API03 | UT14–UT17 |
| Q09 (withdrawal/cancellation) — **not in source** | — no AC | — no endpoint | — no UT |

## Self-Review Against the Day 2 Completion Gate (superseded by Day 3 below, kept for history)

- Intent is one paragraph, states what/for whom/conditions/boundary, no implementation nouns.
- Every AC has a stable ID and Given/When/Then form.
- Every AC traces to BRD-001 (see Traceability) or is explicitly withheld with a reason (Q03, Q09).
- Unconfirmed rules (tenure, lead time, concurrency, downstream-applicability, rollback, RBAC
  mechanism, audit) are recorded as open, not asserted as fact.
- Out of Scope and Non-Functional Constraints sections exist.
- Frontend/backend responsibilities are stated without naming a controller, model, component, or
  library.
- API Contract section is a Day 3 placeholder only.
- No code, `.plan.md`, or `.tasks.md` was created.

## Self-Review Against the Day 3 / Gate 1 Readiness Check

- Intent remains unambiguous and unchanged from Day 2.
- All 17 ACs are individually IDed and testable; none were added or altered on Day 3.
- API contract (API01–API03) has full payloads, success shapes, status codes, and a
  per-endpoint exception table with stable `errorCode` values.
- The contract is framework-neutral — no controller, Eloquent model, migration, or middleware
  class name appears anywhere in it; the base path, error envelope, and 403/404 split are
  explicitly flagged as proposed specification decisions for Gate 1, not repository facts.
- The Next.js Consumption Contract section states what the frontend may rely on and reiterates
  that no authoritative business rule is reimplemented client-side.
- All 17 UTs map 1:1 to the 17 ACs (`UT01`↔`AC01` … `UT17`↔`AC17`); none were forced to a fixed
  count.
- Broader QA/developer scenarios exist in `.ai-context/test_cases/emp-internal-transfer.test_cases.md`,
  and do not duplicate this file's compact UT table.
- Unsupported business rules were not invented: no duplicate/concurrency exception (Q03), no
  eligibility-criteria assertion beyond gate existence (Q01), no minimum-lead-time validation
  (Q02), no downstream-applicability rule (Q06/Q07), no audit-log requirement.
- No secrets, credentials, or employee PII appear in this spec or in `test_cases/`.
- No `.plan.md`, `.tasks.md`, migration, controller, service, React component, or production code
  was created.
- Status is `In Peer Review (Gate 1)`. Gate 1 is **not** claimed as Approved — Sourav Kumar Maity
  is recorded above as the assigned reviewer (confirmed distinct from the author, Aditya Hazra);
  this document awaits their Gate 1 decision.
