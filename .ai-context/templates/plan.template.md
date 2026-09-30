# Plan: <Feature Name>

## Derived From
.ai-context/specs/<feature-slug>.spec.md (Status must be **Approved** before this plan is written)

## Architecture Approach
<Components touched, new vs. existing, integration points. Name them explicitly — do not leave
anything for the agent to infer at implementation time.>

- Laravel layer(s) touched: `app/Http/Controllers/...`, `app/Http/Requests/...`,
  `app/Policies/...`, service/domain layer, `app/Models/...`, `app/Http/Resources/...` — per
  `.agent/rules/int-standards.laravel.md`'s layering table.
- Next.js route(s)/page(s) touched, and server vs. client component boundary — per
  `.agent/rules/int-standards.nextjs.md`.
- Shared infrastructure used: <none yet — no source tree exists in this repo as of this
  template's authoring; update this line once one does>.

## Stack decisions this plan settles
<Every item still marked `[Open]` in the two int-standards.*.md files that this feature actually
needs a real answer for — auth mechanism, API base path/versioning, error envelope shape, Next.js
routing model, language, state/data-fetching approach, test frameworks. State the decision and
update those two files in the same task so the next spec doesn't re-litigate it.>

## Data Model
<Schema changes and migrations, if any. State "no schema change" explicitly when that is the case.
Derive entities/fields only from what the spec's API contract payloads already imply — do not
invent beyond them.>

- Models added/changed: <model names, or none>
- Migration required: <yes/no>
- Relationships to existing entities: <or "none — first feature">

## Constitution Check
<!-- A real verdict per line. Silence on a rule is a gap, not a neutral omission. -->
- [ ] No new datastore, queue, or service introduced without an ADR
- [ ] Layering respected — no business rules in controllers, no ad hoc authorization checks
      outside Policies/Gates/middleware
- [ ] Testing discipline matches constitution.md (test-first, coverage floor, whichever framework
      is scaffolded — see int-standards.laravel.md/int-standards.nextjs.md's "Open" notes)
- [ ] Security posture matches constitution.md (PII masking, no secrets logged, auth/authz on
      every endpoint)
- [ ] Rate-limit decision made explicit for every new or changed endpoint
- [ ] Non-functional baselines respected (p95 latency target, availability target)

## Explicitly Deferred
- <item, with the reason it is deferred and where it is tracked — e.g. a spec Contract Gap, an
  open BRD item>

<!-- Gate 2 verifies that implementation did not scope-creep into what was deferred here. -->

## ADR Impact
<Any decision that would cost more than a day of rework to reverse needs an ADR in
.ai-context/decisions/, not just a line here. State "none" if none.>

## Sequencing
1. <high-level build order — each item must be independently verifiable and mergeable>
2. ...
