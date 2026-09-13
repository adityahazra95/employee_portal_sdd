# Test Cases: Employee Internal Transfer Digital Journey

**Spec:** [`.ai-context/specs/emp-internal-transfer.spec.md`](../specs/emp-internal-transfer.spec.md)
**BRD:** [`BRD-001`](../BRD.md#brd-001-employee-internal-transfer-digital-journey)

This file expands the spec's compact `UT01`–`UT17` table into broader QA/developer scenarios. It
does not restate the specification — read the spec for Intent, full AC text, the API contract, and
the Open Decisions this feature deliberately does not resolve (Q01–Q11 minus Q04/Q10, which don't
gate any AC or scenario here). Every scenario below either maps to a spec AC/API or is explicitly
marked as a **gap scenario** — a case worth testing once an open decision resolves, not something
to implement against today.

---

## 1. Unit / API scenarios

Direct API-level exercises of `API01`–`API03`, beyond the compact `UT01`–`UT17` mapping.

| ID | Maps to | Scenario | Expected |
|---|---|---|---|
| `IT-API-01` | API01 / AC01 | `POST /transfer-requests` with a fully valid payload | `201`; response body matches the documented `data` shape exactly (no undocumented fields) |
| `IT-API-02` | API02 / AC12, AC13 | `GET /transfer-requests/{id}` immediately after creation | `200`; `status: submitted`, `pendingWith: manager`, `stages[]` shows `manager_confirmation: pending` and the rest `not_applicable`/unset as appropriate |
| `IT-API-03` | API03 / AC06, AC08 | Full happy-path walk: submit → manager approve → HR approve → (if downstream applies) mark steps complete → completed | Each `GET` between steps reflects the correct `status`/`pendingWith` for that point in the journey |
| `IT-API-04` | API01 | Response body of a successful create is checked against the JSON Schema implied by the spec's example | No extra fields (e.g. no internal database id, no other employee's data) |

## 2. Validation and boundary tests

| ID | Maps to | Scenario | Expected |
|---|---|---|---|
| `VAL-01` | AC03 | Omit `targetDepartmentId` only (repeat for `targetLocationId`, `targetRoleId`, `effectiveDate`) | `400 validation_error`, that field named in `errors[]`, no others |
| `VAL-02` | AC03 | Submit an entirely empty payload | `400 validation_error`, all four required fields named |
| `VAL-03` | AC04 | Submit a `targetDepartmentId` that is syntactically well-formed but does not exist | `400 invalid_reference_data` |
| `VAL-04` | AC04 | Submit a `targetDepartmentId` that exists but is not currently selectable (e.g. a deactivated department, once reference data supports that concept) | `400 invalid_reference_data` — **gap scenario**: "currently selectable" implies a reference-data lifecycle this spec does not define; test once the reference-data contract gap (see spec) is resolved |
| `VAL-05` | API01 | `effectiveDate` in an invalid format (e.g. `"01/01/2026"`, `"not-a-date"`) | `400 malformed_payload` |
| `VAL-06` | — gap (Q02) | `effectiveDate` set to yesterday, or to today | **Gap scenario.** No lead-time or past-date rule is confirmed — document the system's actual behaviour once Q02 resolves; do not assert rejection or acceptance today |
| `VAL-07` | AC02 | `reason` omitted vs. `reason: ""` vs. `reason: null` | All three should be handled consistently (spec only requires "not required" — verify the three are not silently treated as different states) |
| `VAL-08` | API03 | `decision` field with an unrecognised string (e.g. `"maybe"`) | `400 validation_error` |
| `VAL-09` | API03 | `decision` field omitted entirely | `400 validation_error` |

## 3. Authentication and RBAC tests

| ID | Maps to | Scenario | Expected |
|---|---|---|---|
| `AUTH-01` | API01/02/03 | Call each endpoint with no credentials | `401 unauthenticated` on all three |
| `AUTH-02` | API01/02/03 | Call each endpoint with expired/invalid credentials | `401 unauthenticated` |
| `AUTH-03` | AC14 | Employee A authenticated, requests Employee B's `transferRequestId` via API02 | `403 forbidden`; no leakage of B's data in the error body |
| `AUTH-04` | AC15 | A manager who does **not** manage the requesting employee calls API03 while `pendingWith: manager` | `403 forbidden_wrong_stakeholder` |
| `AUTH-05` | AC16 | An employee without the HR role calls API03 while `pendingWith: hr` | `403 forbidden_wrong_stakeholder` |
| `AUTH-06` | AC17 | A user with no relationship at all (not requester, not manager, not HR) calls API02 and API03 | `403` on both |
| `AUTH-07` | AC15/AC16 | The *correct* manager acts while `pendingWith: hr` (wrong stage for their role) | `403 forbidden_wrong_stakeholder` — being *a* valid manager somewhere is not being *the* stakeholder for the currently pending stage |
| `AUTH-08` | — gap (Q11) | Delegation/out-of-office coverage — a stand-in manager acts on behalf of the assigned manager | **Gap scenario.** Q11 (RBAC resolution mechanism) is open; no delegation behaviour is specified. Test once resolved |

## 4. State-transition tests

| ID | Maps to | Scenario | Expected |
|---|---|---|---|
| `STATE-01` | AC06 | Manager approves a `submitted` request | `status → manager_approved`, `pendingWith → hr` |
| `STATE-02` | AC07 | Manager declines a `submitted` request | `status → manager_declined`, `pendingWith → none`, terminal |
| `STATE-03` | AC08 | HR approves a `manager_approved` request | `status → hr_approved` |
| `STATE-04` | AC09 | HR declines a `manager_approved` request | `status → hr_declined`, `pendingWith → none`, terminal |
| `STATE-05` | AC11 | All applicable downstream steps complete on an `hr_approved`/`downstream_processing` request | `status → completed`, terminal |
| `STATE-06` | API03 exceptions | HR attempts API03 on a request still `submitted` (manager hasn't acted) | `409 invalid_state_transition` |
| `STATE-07` | API03 exceptions | Any actor attempts API03 on a `manager_declined`, `hr_declined`, or `completed` (terminal) request | `409 invalid_state_transition` |
| `STATE-08` | API03 exceptions | Manager attempts to act a second time on a request already `manager_approved` or `manager_declined` | `409 invalid_state_transition` (state has already moved past their stage) |

## 5. Concurrency tests

No formal idempotency-key requirement is confirmed for this feature (unlike financial-mutation
features elsewhere in the constitution's model — this isn't one). These scenarios instead exercise
the `already_processed` vs. `invalid_state_transition` distinction API03 defines for genuine races.

| ID | Maps to | Scenario | Expected |
|---|---|---|---|
| `CONC-01` | API03 exceptions | Two near-simultaneous requests both attempt to be "the manager who approves" the same `submitted` request | Exactly one succeeds (`200`, state moves to `manager_approved`); the other receives `409 already_processed` |
| `CONC-02` | API03 exceptions | A manager submits `decision: approve` while, unknown to them, HR has already been notified is impossible in this flow — but a manager submits `decision: approve` twice in quick succession (double-click) | First succeeds; second receives `409 already_processed` or `409 invalid_state_transition` depending on timing, never a silent duplicate state change |
| `CONC-03` | — gap (Q03) | Employee attempts to submit a second `POST /transfer-requests` while one is already active | **Gap scenario.** Q03 is open — document actual behaviour (likely: a second record is simply created, since no protection is implemented) and flag it back to BRD.md, don't treat either outcome as a pass/fail today |

## 6. Laravel API contract tests

Verifies the Laravel implementation (once it exists) matches this spec's contract exactly —
these are contract/shape tests, not business-logic re-tests already covered above.

| ID | Scenario | Expected |
|---|---|---|
| `CONTRACT-01` | Every success response from API01/02/03 | Matches the documented `data` shape field-for-field; no extra or missing keys |
| `CONTRACT-02` | Every error response | Matches the shared error envelope (`error.errorCode`, `error.message`, optional `error.errors[]`) |
| `CONTRACT-03` | Any `errorCode` value returned by the running implementation | Is one of the stable values this spec defines; a new/undocumented `errorCode` is a spec gap, not a silent addition |
| `CONTRACT-04` | Response headers on every endpoint | `Content-Type: application/json`; no internal headers (e.g. framework version) leaked |
| `CONTRACT-05` | Any 4xx/5xx body | Contains no PII beyond what that endpoint's own success response already authorizes, no stack trace, no SQL, no file paths — per `constitution.md` |

## 7. Next.js integration / consumer tests

| ID | Scenario | Expected |
|---|---|---|
| `FE-01` | Submit the transfer request form with all fields valid | Frontend calls API01, shows a success state, and navigates to/renders the status view using API02's `data` shape directly (no client recomputation of `status`) |
| `FE-02` | Submit with a required field missing | Frontend surfaces the exact field(s) named in `errors[]` next to the corresponding input, without inventing its own duplicate validation message |
| `FE-03` | Status view renders for each of the 7 `status` enum values | Each renders a distinct, recognisable UI state; an unrecognised `status` value renders an explicit "unexpected state" indicator, not a blank screen |
| `FE-04` | Manager/HR viewing a request where `pendingWith` is not their stage | The UI does not present an approve/decline control for a stage that isn't theirs to act on (courtesy only — see FE-08 for the enforcement check) |
| `FE-05` | API call returns `401` | Frontend redirects to sign-in / re-auth flow, not a raw error dump |
| `FE-06` | API call returns `403` | Frontend shows an authorization-appropriate message, not the raw `errorCode` string |
| `FE-07` | API call returns `500` | Frontend shows a generic failure state; no internals from the response are rendered |
| `FE-08` | A user manipulates the client (e.g. browser devtools) to invoke an action the UI hid per FE-04 | API03 still independently rejects it (`403 forbidden_wrong_stakeholder`) — proves the frontend never became the enforcement point |

## 8. Loading / error / empty-state UI scenarios

| ID | Scenario | Expected |
|---|---|---|
| `UI-01` | Form submission in flight | Submit control shows a busy/disabled state; no double-submit possible from a repeated click |
| `UI-02` | Status view while API02 request is in flight | A loading placeholder, not a blank or stale-data flash |
| `UI-03` | Status view for a request with no downstream steps applicable | `stages[].steps[]` empty/`not_applicable` renders as "no further steps," not as a loading state that never resolves |
| `UI-04` | Any of the three endpoints times out or the network fails | A distinct "couldn't reach the server" state, separate from a `500` from the server itself |

## 9. Accessibility / form interaction checks

| ID | Scenario | Expected |
|---|---|---|
| `A11Y-01` | Every request-form field | Has an associated `<label>` (or equivalent accessible name) |
| `A11Y-02` | Field-level validation error from `errors[]` | Is programmatically associated with its input (e.g. `aria-describedby`), not conveyed by color alone |
| `A11Y-03` | Full request form | Operable by keyboard alone, including date input and submit |
| `A11Y-04` | Approve/decline action for manager/HR | Reachable and operable by keyboard; result is announced (not just visually updated) for screen-reader users |

## 10. Cross-browser / device coverage

No existing frontend QA standard exists in this repository to inherit (no Next.js source tree yet
— see `.agent/rules/int-standards.nextjs.md`). Pending that standard being established at Day 5 or
during actual frontend build-out, treat the following as the minimum until superseded:

| ID | Scenario |
|---|---|
| `XBROWSER-01` | Request form and status view render and function on the two most recent versions of Chrome, Edge, and Safari |
| `XBROWSER-02` | Request form and status view are usable at a mobile viewport width (this is an employee self-service portal; mobile use is plausible even though the BRD doesn't state a device requirement) |

## 11. Regression considerations

This is the **first feature** specified for the One-Point Employee Portal — there is no existing
Laravel or Next.js codebase, and therefore no existing portal capability this feature could
regress. This section exists as a placeholder for future features: once a second feature is built
alongside this one (e.g. any other employee-portal capability sharing authentication, the employee
profile, or the reference-data endpoints this feature's Contract Gaps section flags), add its
regression scenarios here rather than starting a parallel tracking mechanism.

---

## Traceability back to the spec

Every ID above maps to a spec AC/API section, or is explicitly marked a **gap scenario** tied to
one of BRD-001's open items (Q02, Q03, Q11). No scenario in this file asserts behaviour for an
open decision as though it were resolved — see
[`emp-internal-transfer.spec.md`'s Open Decisions table](../specs/emp-internal-transfer.spec.md#open-decisions-carried-into-this-spec)
for the authoritative list.
