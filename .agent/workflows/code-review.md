# Workflow: /code-review (Gate 2)

Per the INT SDD Blueprint §20/§30: Gate 2 carries the same weight for security as it does for
functional correctness, and is never skipped — including under hotfix pressure (§24, reviewed
within 24 hours instead).

## Prompt template

```
Review <slug>.T0N against its spec and plan.

Read, in this order:
- .ai-context/tasks/<slug>.tasks.md           (T0N's Acceptance mapping)
- .ai-context/specs/<slug>.spec.md            (the AC(s)/API contract T0N must satisfy — read
                                                for "does this diff actually do what the spec
                                                says," not just "is this readable")
- .ai-context/plans/<slug>.plan.md            (constitution-compliance decisions the plan already
                                                made — do not re-litigate them here, verify the
                                                diff matches them)
- .ai-context/constitution.md                 (Security Posture and Architectural Constraints —
                                                check the diff against these directly, not just
                                                against the plan's summary of them)
- The diff itself

Check, in order:
1. Every AC named in T0N's Acceptance line is verified individually, by ID — not "looks
   reasonable."
2. Tests were written first and confirmed Red before this implementation (ask if not evidenced —
   do not assume).
3. Security checklist (below).
4. No AI-attribution in comments or commit messages — a task-ID reference (Implements <slug>.T0N)
   is traceability, not attribution, and is fine.
5. architecture.md / an ADR updated if this diff introduced a new integration, datastore, or
   significant decision.
6. .ai-context/status.md and the spec's Status updated same day.
```

## Security Checklist (used at every Gate 2, and on every hotfix)

- [ ] No PII in logs at any level, including debug — check `constitution.md`'s PII list against
      every log line the diff adds (transfer request data is employee-identifying by nature; this
      feature especially).
- [ ] No secrets, credentials, or tokens hardcoded or logged.
- [ ] Every new/changed endpoint has an explicit rate-limit decision — "none, because X" is valid;
      silence is not.
- [ ] Authentication and authorization present on every endpoint that isn't deliberately and
      explicitly public.
- [ ] New dependencies vetted (maintained, real, approved) before entering `composer.json`/
      `package.json` — per each stack's int-standards.md Guardrails section.
- [ ] Auth boundaries and least-privilege checked against the spec's authorization scenarios
      (e.g. for this feature: requester/manager/HR isolation, and — once built — the downstream-
      step actor boundary from API04), not assumed from the plan alone.
- [ ] SAST/DAST and dependency scanning run and clean, or exceptions explicitly signed off.

## Notes

- A diff that satisfies its ACs but silently resolves an Open Decision the spec left open (e.g.
  inventing a concrete rule for something flagged Q07/Q08/Q12/Q13 in this feature) fails review —
  send it back to the spec, don't let the implementation quietly become the decision.
- Companion workflows: `/generate-plan.md`, `/generate-tests.md` (upstream of this one in the
  chain).
