# Workflow: /generate-plan

Per the INT SDD Blueprint §13/§29: use only once a spec is **Approved** at Gate 1 — never before,
and never against a spec still `In Peer Review`, `Changes Requested`, or carrying unresolved
conditions from a review. A plan derived from an unapproved spec inherits whatever ambiguity Gate 1
would otherwise have caught (Blueprint §3).

## Prompt template

```
Generate the plan for <slug>.

Read, in this order:
- .ai-context/specs/<slug>.spec.md          (the approved spec — the contract; confirm Status
                                              is Approved before proceeding)
- .ai-context/constitution.md               (non-negotiables the plan must not violate or stay
                                              silent on)
- .ai-context/BRD.md                        (confirm which open items the spec carried forward —
                                              the plan must not silently resolve a business
                                              question the spec left open)
- .agent/rules/int-standards.laravel.md and int-standards.nextjs.md
                                             (current stack conventions — check whether any
                                              `[Open]` item the spec flagged has since been
                                              settled by an earlier feature's plan)
- <the specific existing modules this feature touches, once any exist>

Produce .ai-context/plans/<slug>.plan.md using .ai-context/templates/plan.template.md.

Requirements:
- Name integration points and the data model explicitly. Do not leave anything for the
  implementation task to infer.
- Settle every stack decision the spec left `[Open]` that this feature actually needs (auth
  mechanism, API base path, error envelope shape, etc.) — state the decision here and update the
  two int-standards.*.md files in the same task, not silently assume one.
- For every Open Decision the spec carried forward from BRD.md, state how the plan handles it
  *without* resolving the underlying business question — e.g. "returns 409 and does not attempt
  rollback, because the rollback rule (Q08) is still open" is valid; quietly implementing a
  rollback is not.
- Check .ai-context/inventory/ (if populated) or run a discovery pass over existing code first —
  do not silently duplicate a module or endpoint another feature already owns.
- List every ADR candidate per constitution.md's Architectural Constraints (new datastore, new
  external service, a second library filling an existing role) — do not decide these unilaterally
  without flagging them.
- State what this plan explicitly defers, and why.
```

## Notes

- One plan, one spec — do not fold two features' plans together even if they touch the same
  module; that's what the "Related specs" cross-reference is for.
- If the plan would violate or stay silent on a constitution.md rule, stop and raise it — per
  Blueprint §13, that's a Gate 1 rejection, not something to quietly work around.
- Companion workflows: `/generate-tests.md` (next step, from this plan's own Tasks), `/code-review`
  (used at Gate 2 once implementation exists).
