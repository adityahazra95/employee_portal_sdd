# Workflow: /generate-tests

Per the INT SDD Blueprint §5 (Principle 3) and §18: no implementation code before the corresponding
tests exist, are reviewed, and are confirmed to **fail** (Red phase). This workflow generates that
first Red pass — never tests retrofitted after implementation.

## Prompt template

```
Generate the test-first (RED) tests for <slug>.T0N.

Read, in this order:
- .ai-context/tasks/<slug>.tasks.md          (confirm T0N's Acceptance mapping — which AC/UT
                                               IDs this task must satisfy)
- .ai-context/specs/<slug>.spec.md           (the AC's Given/When/Then and, for endpoint-facing
                                               tasks, the API Contract's request/response/
                                               exception shapes)
- .ai-context/test_cases/<slug>.test_cases.md (broader scenario coverage beyond the spec's
                                               compact UT table — boundary, negative, auth/RBAC,
                                               concurrency cases this task's tests should also
                                               cover if in scope for T0N)
- .agent/rules/int-standards.laravel.md or int-standards.nextjs.md (whichever this task's layer
                                               is in) — confirm the actual test framework once
                                               scaffolded; do not assume PHPUnit vs. Pest, or a
                                               frontend framework, without checking

Write tests only for the UT IDs T0N's Acceptance line names — do not opportunistically cover
adjacent ACs from other tasks.

Requirements:
- One test per Given/When/Then branch the AC/UT actually states — including the negative and
  boundary conditions the spec's UT table calls out (e.g. UT03/UT04's parameterised cases), not
  just the happy path.
- Assert observable outcomes only — HTTP status, `errorCode`, response shape, state transition —
  never implementation internals (a private method was called, a specific query ran).
- Run the tests and confirm they FAIL before writing any implementation. A test that passes before
  the implementation exists is testing nothing (spec's own note on this, and Blueprint §5).
- No PII, secrets, or real employee data in any test fixture — synthetic data only.
- Reference the AC/UT ID in the test name/description so a failure traces back to the spec, not
  just to a file and line number.
```

## Notes

- Do not write tests for an AC the spec has flagged as an Open Decision with "no AC written" (e.g.
  Q03's duplicate-request behaviour, Q09's withdrawal) — there's nothing confirmed to test yet.
- If a test can't be written because the spec's AC is ambiguous, that's a spec gap, not a testing
  problem — stop and raise it rather than guessing at the intended behaviour.
- Companion workflows: `/generate-plan.md` (produces the plan this task's tasks.md derives from),
  `/code-review.md` (Gate 2, once the Green implementation exists).
