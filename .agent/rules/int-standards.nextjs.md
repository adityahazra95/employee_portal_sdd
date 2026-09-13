# INT Standards — Next.js / React (One-Point Employee Portal Frontend)

Always-on rules for this repository's frontend. The agent reads this every session, regardless of
task. Feature-specific rules belong in that feature's spec, not here.

> **Repository state (recorded 2026-09-03):** no Next.js/React source tree exists in this
> repository yet — no `package.json`, no `next.config.*`, no `app/`/`pages/` directory. The items
> below are therefore **defaults and open questions, not repository-verified choices.** The first
> spec that touches frontend code must record the actual Next.js version, routing model,
> language, state/data-fetching approach, and test framework as soon as the project is scaffolded,
> and this file must be corrected to match reality at that point — do not let this note go stale.

## Open, not to be silently decided by an agent

These are stack/architecture decisions the Day 5 technical plan owns, not Day 1 discovery. Do not
pick one while writing a spec or plan; raise it as an explicit decision instead.

- **Routing model** — App Router vs. Pages Router is unset. Whichever is scaffolded first is
  binding; the other must never be introduced alongside it.
- **Language** — TypeScript vs. JavaScript is unset.
- **State / data-fetching** — no library chosen (e.g. React Query/SWR/RTK Query, or plain
  fetch + server components). Once one exists, do not add a second for this feature.
- **UI system** — no component/design-system library chosen.
- **Test framework** — no frontend test setup exists (Jest, Vitest, Testing Library, Playwright,
  etc. are all unverified here).
- **API client convention** — how the frontend calls the Laravel API (generated client, hand-written
  fetch wrapper, etc.) is unset.

## Architecture boundaries

- Server/client component boundaries (once App Router is in use) must follow whatever convention
  the first scaffolded pages establish — do not mix conventions within one feature.
- The Next.js app consumes the Laravel API's **approved contract** only. It does not own business
  rules, authorization decisions, or state-transition logic for the transfer journey — those are
  the Laravel API's responsibility per the constitution's backend/frontend boundary.
- Route-level access control in the frontend is a UX convenience (e.g. redirecting an unauthorized
  user away from a page); it is never the enforcement point. The API enforces authorization
  regardless of what the frontend renders.

## UI and state conventions

- Every form/state-changing interaction needs explicit loading, error, and empty states — "what
  does the screen show while the request is in flight / if it fails / if there's nothing to show"
  is an acceptance criterion, not an implementation afterthought.
- Authorization-aware UI: a screen or action not permitted to the current user's role must not be
  silently rendered and then rejected by the API as the only signal — the UI reflects the
  permission state it already knows (e.g. from the session/profile response).
- Forms must be accessible: labelled inputs, keyboard operability, and error messages associated
  with their field (not just a color change or a toast that vanishes).

## Security

- No secrets, tokens, or employee PII (name, mobile number, email, department, location, salary
  data) in client-side `console.log`/analytics events. Debug logging that would expose any of this
  in a browser console or a shipped source map is a defect.
- Any token used to call the Laravel API is handled per whatever the chosen auth mechanism
  requires (e.g. httpOnly cookie vs. bearer token in memory) — never stored in `localStorage`
  without that decision being recorded and justified, since it is XSS-exposed.

## Documentation and tests

- Test-first, once a frontend test framework is scaffolded: the test exists, is reviewed, and is
  confirmed failing before implementation.
- Do not introduce a second state, data-fetching, UI, or testing library merely for this feature —
  reuse whatever the repository establishes first.

## Guardrails

- No AI attribution in code comments or commit messages. A task-ID reference
  (`Implements <slug>.T03`) is traceability and is encouraged.
