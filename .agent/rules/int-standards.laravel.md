# INT Standards — Laravel / PHP (One-Point Employee Portal API)

Always-on rules for this repository's backend. The agent reads this every session, regardless of
task. Feature-specific rules belong in that feature's spec, not here.

> **Repository state (recorded 2026-09-03):** no Laravel/PHP source tree exists in this repository
> yet — no `composer.json`, no `artisan`, no `app/` directory. The items below are therefore
> **defaults carried over from INT's standard Laravel conventions, not repository-verified
> choices.** The first spec that touches backend code must record the actual PHP/Laravel version,
> package set, auth mechanism, and test framework as soon as the project is scaffolded, and this
> file must be corrected to match reality at that point — do not let this note go stale.

## Open, not to be silently decided by an agent

These are stack/architecture decisions the Day 5 technical plan owns, not Day 1 discovery. Do not
pick one while writing a spec or plan; raise it as an explicit decision instead.

- **Auth mechanism** — Sanctum, Passport, and session-based guards are all valid Laravel choices
  with different trade-offs for a first-party SPA-style Next.js frontend vs. third-party API
  consumers. No mechanism is chosen yet.
- **PHP/Laravel version** — to be pinned once the project is scaffolded; do not assume a specific
  minor version in a spec.
- **Test framework** — PHPUnit ships with Laravel by default; Pest is a common alternative. Neither
  is chosen yet. Whichever is scaffolded first is the one every subsequent spec must use — do not
  introduce a second one later without an ADR.

## Layering — never blur these

| Layer | Path | Allowed to contain |
|---|---|---|
| HTTP | `app/Http/Controllers/**` | Route handling and orchestration only — no business rules |
| Validation | `app/Http/Requests/**` | Form Request classes; every request body/query validated here, not in the controller |
| Authorization | `app/Policies/**`, `app/Http/Middleware/**` | Policy/Gate checks and route-level middleware — never an ad hoc `if` in a controller |
| Domain | `app/Services/**` (or equivalent domain layer once established) | Business logic, orchestration across models |
| Persistence | `app/Models/**` | Eloquent models; query logic that isn't a one-liner belongs in a repository/query-object once that pattern exists in the repo |
| Serialization | `app/Http/Resources/**` | API Resources — the only place a model is shaped into an API response |

- Controllers must not contain business rules, must not query the database directly, and must not
  hand-build response arrays where an API Resource applies.
- Authorization is enforced through Policies/Gates or middleware, never a controller-level ad hoc
  permission check.

## Validation and error handling

- Every request body, query, and route param is validated through a Form Request class. A
  controller method that reads `$request->input(...)` without a corresponding Form Request is a
  defect.
- Use Laravel's standard exception-to-JSON-response handling (`app/Exceptions/Handler.php` or the
  framework's current equivalent). Do not invent a second error envelope shape without recording it
  as a decision — the exact envelope is TBD until the first spec that returns an error response
  defines it.
- Every protected route must carry both authentication (via the chosen auth mechanism) and
  authorization (Policy/Gate/middleware) — a route with neither is a defect, not an oversight to
  fix later.

## Data layer

- Access the database only through Eloquent/query builder with parameter binding. Raw SQL string
  concatenation of any user-supplied value is prohibited.
- Schema changes go through Laravel migrations. No hand-edited schema against a shared environment.
- Use API Resources (`php artisan make:resource`) for every response shape; do not return raw
  Eloquent models from a controller.

## Security

- **No PII in logs at any level, including debug.** PII includes: full name, mobile number, email,
  government ID, payment instrument details, and full postal address. Mask or omit before logging.
- Secrets come from environment variables (`.env`, never committed) and are read through Laravel's
  config layer (`config/*.php` + `env()`), never referenced with a raw `env()` call outside
  `config/`.
- Authentication and authorization are required on every route that is not explicitly and
  deliberately public — "public because X" is a recorded decision, not a default.
- Rate-limiting is an explicit per-route decision (Laravel's built-in throttle middleware or
  equivalent). "No limit, because X" is an acceptable decision; silence is not.
- Vet any suggested Composer package before adding it; prefer packages already in `composer.json`
  once one exists.

## Documentation and tests

- Test-first: the test exists, is reviewed, and is confirmed failing before implementation, using
  whichever framework (PHPUnit/Pest) is scaffolded first — see "Open, not to be silently decided."
- API contract (OpenAPI or equivalent) is updated in the same task as any endpoint, payload, or
  response change, once a contract format is established.

## Guardrails

- Do not introduce a second ORM, validation approach, or HTTP client convention once one exists in
  the repository.
- Do not introduce a second test framework without an ADR.
- No AI attribution in code comments or commit messages. A task-ID reference
  (`Implements <slug>.T03`) is traceability and is encouraged.
