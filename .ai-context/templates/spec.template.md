# Spec: <Feature Name>

> Base structure transcribed from the INT SDD Blueprint (`source-docs/INT SDD BluePrint - V1.0.pdf`,
> §11.4/§29) — the actual standard this project runs under, not a derived example. Sections below
> marked **(project addition)** are not in the Blueprint's minimum template; they were adopted for
> `emp-internal-transfer` and proved useful enough (in particular, surviving a real Gate 1 review
> intact) to keep as this project's standard shape. Amend this file only by agreement between the
> Spec Author and Gate 1 Reviewer.

## Spec ID
<feature-slug>

## Status
<Draft vX.Y / In Peer Review (Gate 1) / Changes Requested / Approved / Plan Drafted / Plan
Reviewed / Tasks Generated / In Development / In QA / Ready for Release / Released (vX.Y.Z) /
Deprecated-Superseded — per Blueprint §21.1. Nothing skips a state. On a review outcome other than
a clean Approved, bump the minor version marker (e.g. `Draft v1.1`) per §12.3, rather than
inventing a new status label.>

## Gate 1 Review (project addition)
**Author:** <name> · **Gate 1 Reviewer:** <name — must not be the author, per Blueprint §12.1> ·
**Review Date:** —

Record the reviewer's comments **verbatim**, not paraphrased or silently resolved. Blueprint §12.3
outcomes are binary: `Approved` or `Changes Requested` — don't invent a third state. Cross-reference
each observation to an existing `BRD.md` open item where one exists; flag anything genuinely new so
it gets added to `BRD.md`'s Open Decisions table, not left stranded in review prose only. If a
revision follows, add a **Revision Notes** subsection here dispositioning every item point-by-point
(disposition = fixed directly / controlled assumption adopted / recorded as a new BRD open item) —
see `emp-internal-transfer.spec.md`'s v1.1 revision for the worked example.

## Linked BRD
.ai-context/BRD.md#<brd-anchor>

## Intent
<One paragraph: what changes, for whom, under what condition. No implementation detail — no
controller, Eloquent model, database table, React component, or library name belongs here; that's
the plan's job.>

## Context
- Builds on: `.ai-context/constitution.md` (non-negotiables this spec must not violate or stay
  silent on)
- Related: `.ai-context/specs/<related-spec>.spec.md` — check for existing modules/endpoints this
  feature might reuse, extend, or duplicate before re-specifying something another feature owns
- API contract (if consuming an external one): <path/link>

## API Contract
<Mandatory only if this feature has an API surface — an endpoint it exposes or a contract it
consumes (Blueprint §11.3). Framework-neutral: product-level request/response shapes, not
controller/model/migration detail.>

### <slug>.API01 — <METHOD> <path>
**Request payload:**
```json
{ "field": "type" }
```
**Success response (<code>):**
```json
{ "field": "type" }
```
**Exceptions:**
| Code | Condition | Response body |
|---|---|---|
| 4xx | <condition> | <shape> |

## Acceptance Criteria
1. <slug>.AC1 — Given <state>, when <action>, then <outcome>.
2. ...

## Unit Test Cases (spec-derived)
| Test ID | Maps to AC | Scenario | Expected |
|---|---|---|---|
| <slug>.UT01 | AC1 | <scenario> | <expected> |

## Explicitly Out of Scope
- <item>

## Non-Functional Constraints (from constitution.md)
- <latency / availability / PII / auth / rate-limit / coverage — cite the constitution directly,
  don't restate numbers that might drift>

## Open Decisions Carried Into This Spec (project addition)
Table: `Ref | Item | Affects`. None of these may be silently implemented against — cross-reference
`BRD.md`'s Open Decisions table rather than duplicating its detail.

## Frontend/Backend Responsibility Boundary (project addition — for any feature spanning both)
Laravel API = authoritative (validation outcome, authorization, state, status/timeline data).
Next.js = presentation and client interaction against the approved contract only. State this
concretely for this feature's actual endpoints, not as a restatement of the general rule.

## Traceability (project addition)
`BRD source | AC | API | UT` — every row, including ones deliberately absent (open item, no
AC/endpoint/test written).

## Self-Review Against the Completion Gate (project addition)
Per Blueprint §27 Definition of Ready: Intent is one unambiguous paragraph; every AC is
individually IDed and given/when/then; API Contract section present and complete if applicable;
context artefacts linked; out-of-scope items named; non-functional constraints named and sourced;
Gate 1 reviewer assigned and is not the author; Status is `Approved`, not `Draft`/`Changes
Requested`, before a plan gets drafted.
