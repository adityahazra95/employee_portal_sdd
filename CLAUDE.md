# One-Point Employee Portal

This project runs **Specification-Driven Delivery (SDD) v1.0**. Code is generated output; the
specification is the primary artefact. Nothing gets implemented by prompting against the codebase.

Before doing any work here — answering questions, reviewing code, or implementing anything — read
[.ai-context/project_context.md](.ai-context/project_context.md) first. It is the mandatory index
into the constitution, requirement baseline, per-feature artefacts, and current state.

Then follow, without exception:

- [.ai-context/constitution.md](.ai-context/constitution.md) — the non-negotiables. A plan that
  violates or stays silent on any line here is a Gate 1 rejection.
- [.agent/rules/int-standards.laravel.md](.agent/rules/int-standards.laravel.md) and
  [.agent/rules/int-standards.nextjs.md](.agent/rules/int-standards.nextjs.md) — always-on coding,
  layering, security, and guardrail rules for each stack half. Both currently document defaults,
  not repository-verified conventions — no Laravel or Next.js source tree exists in this repo yet.
- [.agent/rules/auto-log.md](.agent/rules/auto-log.md) — append to `prompt_history.md` and update
  `status.md` the same day, every time.

**Hard rules that stop work:**

- No implementation without an **Approved** spec in `.ai-context/specs/`, and an approved
  `tasks.md` derived from a reviewed plan. Work one task at a time, referenced by ID.
- Test-first. Tests exist, are reviewed, and are confirmed **failing** before implementation.
- Prompt by identity (`<slug>.T03`), never by re-describing the feature.

Do not treat this file as a summary — it intentionally contains almost nothing else, because
`.ai-context/` and everything it indexes is the actual source of truth and is kept current.
Duplicating any of it here would just create a second place for it to go stale.

> **History note:** this repository originally ran a different project ("Empty Floor + Circle
> Tap"). It was repurposed for the One-Point Employee Portal on 2026-09-03; the prior project's
> content was removed from the repo once confirmed unneeded (see `project_context.md` and
> `status.md`). If you're looking for it, it isn't here on purpose, not by accident.
