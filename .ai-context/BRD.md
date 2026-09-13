# BRD — Business Requirements

Where a requirement is **first written down**, before it becomes a spec. A spec must never be the
first place a requirement appears. Entries are numbered, never renumbered, and never deleted —
superseded entries are marked, not removed.

> **Reset note (2026-09-03):** this file previously held BRD-001…012 for an unrelated project
> ("Empty Floor + Circle Tap"). That content is archived at
> [`_archive/empty-floor-legacy/BRD.md`](_archive/empty-floor-legacy/BRD.md) and does not apply
> here. Numbering restarts at BRD-001 for this project.

**Source document:** `source-docs/Requirement for SDD.docx` — "SDD Developer Assessment: Employee
Internal Transfer Digital Journey." This is the **only** authoritative source consulted for this
BRD. It is an assessment brief, not a signed-off enterprise requirement with a named business
sponsor — there is no separate FRS/journey-map document set the way the archived project had.

**Owner:** TBD (no PM assigned) · **Status legend:** `Open` · `Decided` · `Superseded`

---

## Discovery rule applied to this BRD

The source document states the business need and the employee-facing capabilities explicitly, but
does **not** specify most of the operational rules that commonly accompany a transfer workflow
(tenure eligibility, minimum notice period, concurrency limits, SLAs, geographic scope, exact
approval sequence beyond "manager confirms, HR validates"). Per the discovery rule for this
exercise, none of the following are treated as confirmed decisions unless traced to the source
document: **minimum tenure, minimum effective-date lead time, one-active-transfer-at-a-time,
approval SLA/reminders, domestic-only scope, or an exact approval sequence beyond what §2/§3 of
the source state.** They are listed under Open Decisions / Assumptions / Proposed Rules below, each
with an owner and impact, and must not be silently assumed by a later spec.

---

### BRD-001: Employee Internal Transfer Digital Journey

**Raised by:** `source-docs/Requirement for SDD.docx` §2 (Business Context) and §3 (Business
Requirement)

**Business problem / objective:** An employee requesting an internal transfer today has to
coordinate manually across multiple teams and systems — discussing with their manager, waiting on
HR eligibility validation, and separately on organisational-information, payroll, IT, and
facilities updates — with no single place to see where the request stands. The organisation wants
the One-Point Employee Portal to provide **one digital journey** for this, and to give the employee
**a single view of progress** across everyone else's pending actions.

**Primary actors** (explicitly named in the source document):

| Actor | Role in the journey |
|---|---|
| **Employee / requester** | Initiates the request, provides its details, views status and pending actions |
| **Manager** | Confirms/approves the transfer |
| **HR** | Validates eligibility |
| **Downstream stakeholders** | Organisational-information owner (updates employee's org record), **Payroll** (updates payroll where applicable), **IT** (provisions/removes access where applicable), **Facilities** (arranges new location where applicable) |

The source document does not name a system/role that owns "organisational information update" as
distinctly as it names Payroll/IT/Facilities — it is listed as a journey step, not clearly assigned
to a named team. Flagged as an open question below (Q02).

**Journey stages** (source §2, in the order given):

1. Request Initiation — employee submits the request.
2. Manager confirmation/approval.
3. HR eligibility validation.
4. Organisational information update.
5. Payroll update, **where applicable**.
6. IT provisioning/removal, **where applicable**.
7. Facilities/new-location arrangement, **where applicable**.
8. Employee confirmation/completion.

The source document itself qualifies stages 5–7 with "may need to" / "where applicable" — it does
not state the rule that decides when each applies. That rule is not defined anywhere in the source
and is an open question (Q03), not something this BRD should invent.

**Employee capabilities** (source §3, explicit):

- Select the proposed new department/business unit.
- Select the proposed new location.
- Select the proposed new role/job position.
- Provide an effective date.
- Provide an optional reason for the transfer.
- Submit the request.
- View the current status of the request.
- View actions that are pending with other stakeholders.

**Sponsor:** Not named in the source document — this is an assessment brief, not a client-sponsored
BRD. Owner recorded as TBD.

**Priority:** Not stated in the source; treated as the sole in-scope feature for this workstream.

**Status:** Decided (core journey and employee capabilities, as listed above, are stated directly
in the source). Everything else about *how* each stage behaves is Open — see below.

---

## Business decisions vs technical decisions

**Business decisions** (what the organisation wants, independent of how it's built):

- A single digital journey replaces manual multi-team coordination for internal transfers.
- The employee chooses the proposed department, location, and role, and provides an effective date
  and optional reason.
- The employee can see current status and which stakeholder an action is pending with, at any time.
- Manager confirmation and HR eligibility validation are both required steps, in that order relative
  to each other (manager before HR, per the source's step order) before downstream execution.
- Payroll, IT, and Facilities involvement is conditional ("where applicable") rather than universal
  for every transfer.

**Technical decisions** (explicitly out of scope for Day 1–3; belong to the Day 5 technical plan):

- Whether org-info/payroll/IT/facilities updates are real system integrations, queued
  work-items for a human team, or a mix — the BRD states the *business* need for orchestration and
  a progress view; *how* that orchestration is implemented (sync API calls, async jobs, manual
  ticket creation, etc.) is a technical decision.
- Data model for the transfer request, its state machine, and how "pending with X" is derived and
  displayed.
- Laravel/Next.js implementation specifics (auth mechanism, routing, API contract shape) — see
  `constitution.md` and the two stack-rule files, all deliberately left `[Open]`.

---

## Open Decisions / Assumptions / Proposed Rules

None of the following are confirmed by the source document. Each needs a decision before the spec
that depends on it can be Approved.

| Ref | Item | Classification | Owner | Impact if unresolved |
|---|---|---|---|---|
| Q01 | Minimum tenure before an employee may request an internal transfer | **Assumption candidate — not in source** | PM/HR policy owner (TBD) | Blocks the eligibility-validation acceptance criteria; HR validation step (§2 stage 3) needs a defined rule set, and tenure is a common one, but nothing in the source names it |
| Q02 | Minimum lead time between submission and the requested effective date | **Assumption candidate — not in source** | PM/HR policy owner (TBD) | Blocks effective-date field validation rules |
| Q03 | Whether an employee may have more than one active transfer request at a time | **Assumption candidate — not in source** | PM/HR policy owner (TBD) | Blocks the "submit" acceptance criteria and the request state model |
| Q04 | Approval SLA and reminder/escalation behaviour for manager confirmation and HR validation | **Not in source** | PM/Ops (TBD) | Blocks whether the portal needs a notification/escalation mechanism, or just a passive status view |
| Q05 | Geographic scope — domestic transfers only, or does the portal need to support cross-country/relocation transfers in v1 | **Not in source** | PM (TBD) | Blocks whether Facilities/relocation-specific fields and rules are in scope for v1 |
| Q06 | Exact roles/sequence of "downstream stakeholders" — is org-info update a distinct owning team, or folded into HR's step | **Ambiguous in source** | PM/HR (TBD) | Blocks who the "pending with" status view attributes stage 4 to |
| Q07 | Rule that decides *when* Payroll/IT/Facilities apply to a given transfer (source only says "may need to" / "where applicable") | **Not in source** | PM/HR/IT/Facilities (TBD) | Blocks the state machine — without this rule the portal cannot know which downstream steps to wait on for a given request |
| Q08 | Whether HR can reject a request outright, or only Manager can, and what happens to Payroll/IT/Facilities steps already triggered if a later stage rejects | **Not in source** | PM/HR (TBD) | Blocks rollback/failure-handling acceptance criteria |
| Q09 | Whether the employee can withdraw/cancel a submitted request before it completes | **Not in source** | PM (TBD) | Blocks a capability not listed in source §3 but common to this kind of workflow — must not be assumed in scope |
| Q10 | Data ownership for each downstream update (which system is authoritative for org-info, payroll, IT access, facilities/seating) | **Not in source** | IT/HR/Payroll/Facilities system owners (TBD) | Blocks the integration-contract question in Dependencies below |
| Q11 | RBAC — who can act as "the manager" or "HR" for a given request (reporting-line lookup source, delegation/out-of-office coverage) | **Not in source** | IT/HR (TBD) | Blocks authorization design for the approval steps |

## Dependencies / integrations

- Manager confirmation requires knowing who the employee's manager is — source of that
  relationship (HRIS, reporting-line table, manual entry) is unstated. **Dependency, not yet
  identified.**
- HR eligibility validation implies HR has (or the portal needs) visibility into eligibility data
  (tenure, current role, any active disciplinary/hold status) — source system unstated.
- Payroll, IT, and Facilities steps imply either an integration with each system, or a
  human-in-the-loop task queue per team — **not stated which**, and likely varies per stakeholder.
  Treat as an integration-contract question for the Day 5 plan, not a Day 1–3 assumption.

## Authorization / RBAC questions

- See Q11 above. Additionally: who can view a given transfer request besides the employee, their
  manager, and HR — is there an admin/HR-ops view across all requests? Not stated in source;
  **assume employee + their manager + HR only, until stated otherwise**, since the source only
  describes those three actors interacting with a given request.

## Failure / rollback questions

- See Q08. No source guidance exists on what happens if a downstream step (Payroll/IT/Facilities)
  fails or cannot be completed after HR approval — this must be resolved before any spec defines
  the request's terminal states.

## Out-of-scope items (assumed, pending confirmation)

Not stated as out of scope in the source, but not stated as in scope either — flagged rather than
silently included:

- Bulk/mass transfers (e.g. team reorganisation) — source describes a single-employee-initiated
  journey only.
- Any UI/process for the manager, HR, Payroll, IT, or Facilities side of the journey beyond what is
  needed for the employee's status view to reflect their actions — the source's employee
  capability list (§3) does not describe those other actors' own interfaces, only that they act on
  the request.
- Any transfer type other than internal transfer (e.g. external hire, contractor conversion).

## Items that must be resolved before Gate 1

At minimum, Q06/Q07/Q08/Q11 — these affect the request's core state machine and authorization
model, which the spec's acceptance criteria cannot be written against without an answer or an
explicitly scoped assumption the PM/reviewer accepts at Gate 1. Q01–Q05/Q09/Q10 can plausibly be
carried into the spec as explicitly labelled assumptions if a reviewer accepts that framing, but
must not be silently treated as decided.
