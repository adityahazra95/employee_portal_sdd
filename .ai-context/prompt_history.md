# Prompt History

Session-level agent audit trail, appended after every completed task per
`.agent/rules/auto-log.md`. Distinct from the human-curated daily summary in
[status.md](status.md) — both are maintained.

**Never** record secrets, credentials, tokens, real customer data, or PII. If an instruction
contained any, log its shape, not its value.

---

### 2026-07-25 — sdd-adoption.wave-1
**Prompted by:** Tapash Dutta
**Instruction (summary):** Establish the SDD v1.0 workspace from scratch — `.agent/` control plane
and `.ai-context/` knowledge base — inferring architecture from the running codebase and seeding
requirements from the four source documents. No feature code.
**Artefacts touched:**
`.agent/rules/int-standards.node.md`, `.agent/rules/.agentignore`, `.agent/rules/auto-log.md`,
`.agent/workflows/{code-review,generate-tests,generate-plan}.md`,
`.ai-context/{constitution,project_context,architecture,SRS,BRD,status,prompt_history}.md`,
`.ai-context/templates/*`, `.ai-context/test_cases/_integration.md`,
`.ai-context/source-docs/*`, `CLAUDE.md`
**Outcome:** Workspace created. Architecture derived by inspecting `package.json`, `src/app.ts`,
`src/config/env.ts`, `src/http/register-routes.ts`, `src/lib/otp.ts`, `prisma/schema.prisma`, and
the module/domain trees. Three source-document conflicts resolved (OTP timing, plan naming, admin
signup); BRD seeded with 12 entries and 10 open items. No tests run — no source code changed.
**Follow-up:** TL to confirm `[Provisional]` constitution values. ADR-0001 (cloud provider),
BRD-007 and BRD-008 to be closed before back-fill Wave B. Slug inventory for the retro-spec
back-fill is the first Wave 2 task.

### 2026-07-25 — sdd-adoption.wave-1b
**Prompted by:** Tapash Dutta
**Instruction (summary):** Justify the two-directory split, and decide what `source-docs/` should
hold. Outcome: keep `.agent/` + `.ai-context/`; drop the Business Proposal PDF and extract its
engineering-relevant sections.
**Artefacts touched:** `.ai-context/source-docs/proposal-extract.md` (new),
`.ai-context/source-docs/Business Proposal_...pdf` (removed), `.ai-context/SRS.md`,
`.ai-context/constitution.md`, `.ai-context/status.md`
**Outcome:** `source-docs/` reduced from 25 MB to 9.5 MB. Extract covers client dependencies,
scope-bounding assumptions, exclusions, supported platforms, change control, and IP ownership;
commercials, prototypes, and the escalation matrix omitted — the last because it carries individual
contact details the constitution forbids in repository artefacts. Three conflicts added to the SRS
register (C-08 Developer role, C-09 approval surface, C-10 backup/DR ownership); the constitution's
RPO/RTO line now records that DR sits with the client's infrastructure team, and that only Staging
and Production are in scope. No source code changed; no tests run.
**Follow-up:** PM to confirm whether C-08 (Developer role excluded in the proposal but implemented
in code) is a commercial gap needing a change request.

### 2026-07-25 — sdd-adoption.wave-2-discovery
**Prompted by:** Tapash Dutta
**Instruction (summary):** Three decisions. Approve the constitution baselines as tiered values
(coverage 80/70/60 on changed files only; p95 300/500/2000 ms; availability 99.5%; RPO 15 min with
tiered RTO), keeping them `[Provisional]`. Externalise source PDFs from git while keeping SRS, BRD,
conflict register, and indexes in the repository. Run a Discovery & Inventory phase — module, API,
database, dependency, and wave prioritisation — before any retro-spec authoring, then record the
retro-spec inventory in `status.md` with one row per spec, owner, priority, and target wave.
**Artefacts touched:** `.ai-context/constitution.md`, `.ai-context/SRS.md`,
`.ai-context/source-docs/README.md` (new), `.ai-context/source-docs/*.pdf` (removed),
`.ai-context/inventory/{module-inventory,api-inventory,database-inventory,dependency-map}.md` (new),
`.ai-context/status.md`, `.ai-context/project_context.md`, `.agent/rules/.agentignore`
**Outcome:** Constitution now carries per-context coverage tiers, per-endpoint latency tiers, and
per-system RTO tiers. All four PDFs removed after capturing SHA-256 checksums into a registry with a
revision protocol — `.ai-context/` fell from 9.5 MB to 160 KB; the proposal PDF row has no hash
because it had already been deleted in wave-1b. Discovery produced four inventories: 426 route
handlers vs 300 documented OpenAPI paths, admin 64% of the API surface, 84 models each claimed by
exactly one spec, and a flat dependency graph where only 17 of 52 contexts import another. Retro-spec
inventory published to `status.md`: **96 specs** across five waves with owner, priority, coverage
tier, and blocking dependency. Discovery raised the sizing from ≈115–125 to **≈173 person-days**.
No source code changed; no tests run.
**Follow-up:** PM to fill the five shared-storage links in `source-docs/README.md` — until then
traceability rests on checksums alone. TL/PM to decide whether to accept ~4 months of blocked feature
work, add authors, or phase Waves A–B first. BRD-007 and BRD-008 block 5 Wave B specs. Authoring
starts at **A01 `permission-model-and-checks`**.

### 2026-07-26 — sdd-adoption.phase-1-decision
**Prompted by:** Tapash Dutta
**Instruction (summary):** Approve Constitution, PDF externalisation, inventory, and Discovery
findings. Adopt phased migration — Waves A–B only (38 specs) as Phase 1; keep C–E in inventory as
deferred; full lifecycle per spec; track Discovery findings as engineering risks; keep owner
allocation as evidence only; resolve open BRDs that block Wave B in parallel with foundations.
**Artefacts touched:** `.ai-context/constitution.md`, `.ai-context/status.md`,
`.ai-context/project_context.md`, `.ai-context/BRD.md`, `CLAUDE.md`, `.ai-context/prompt_history.md`
**Outcome:** Amendment 6 recorded as phased — new feature work blocked until Phase 1 (A–B) completes,
not until all 96 specs. Phase 1 sized at ≈67 person-days. Foundation order locked: A01 → A02 → A04 →
A05/A07. RISK-01…07 registered on the status board (RISK-04/05 in Phase 1; RISK-06/07 deferred).
BRD open-item register split into Phase 1 critical path vs deferred. Owner column unchanged. No
source code changed; no tests run; no specs authored yet.
**Follow-up:** Author A01 `permission-model-and-checks`. PM advances BRD-007/008/009/011 with Client.

### 2026-07-26 — permission-model-and-checks.n/a
**Prompted by:** Tapash Dutta
**Instruction (summary):** Establish the mandatory spec template (all listed sections), then author
A01 `permission-model-and-checks` as the reference implementation; after Gate 1 Approval it becomes
canonical unless the template is explicitly amended.
**Artefacts touched:** `.ai-context/templates/spec.template.md`,
`.ai-context/specs/permission-model-and-checks.spec.md` (new), `.ai-context/status.md`,
`.ai-context/project_context.md`, `.ai-context/prompt_history.md`
**Outcome:** Template rewritten with 19 mandatory sections through Gate 1 checklist. A01 Draft v1.0
documents as-built permission catalogue, `RolePermissionMapping`, effective-key union, Redis cache
(TTL 300s) + DB fallback, `requirePermission` AND semantics, and API01–API05. Risks R01–R04 and
RISK-01/03 tracked; Q01–Q03 do not block Gate 1. No source code changed; no tests run.
**Follow-up:** Gate 1 review by Abhijit Adhikari. On Approved → generate plan then tasks. Do not
treat A01 as canonical template until Approved.

### 2026-07-26 — roster.author-reviewer-amendment
**Prompted by:** Tapash Dutta
**Instruction (summary):** Change project Spec Author to Sourav Kumar Maiti (Senior Software
Engineer) and Gate 1 Reviewer to Tapash Dutta (TL); apply scope C (project defaults + A01 +
everywhere Author/Reviewer is defined).
**Artefacts touched:** `.ai-context/constitution.md`, `.ai-context/project_context.md`,
`.ai-context/specs/permission-model-and-checks.spec.md`, `.ai-context/templates/spec.template.md`,
`.ai-context/status.md` (96 inventory Owner cells → Sourav; Active Specs; Daily Log),
`.ai-context/prompt_history.md`
**Outcome:** Default Author/Reviewer roster updated. Constitution deviation #1 closed (SSE authors,
TL reviews). Abhijit remains PM for BRD/product, not default Gate 1. A01 Author/Reviewer and §19
checklist self-check updated. Historical prompt_history entries left as written (audit trail).
No source code changed; no tests run.
**Follow-up:** Gate 1 on A01 is now Tapash Dutta. Sourav owns subsequent spec authoring under
Phase 1.

### 2026-07-26 — permission-model-and-checks.gate-1
**Prompted by:** Tapash Dutta (Gate 1 reviewer)
**Instruction (summary):** Execute A01 Gate 1 review session plan — §19 checklist items 1–13,
then record Approved or Changes Requested and sync status/prompt_history.
**Artefacts touched:** `.ai-context/specs/permission-model-and-checks.spec.md`,
`.ai-context/status.md`, `.ai-context/prompt_history.md`
**Outcome:** All 13 checklist items **Pass**. Gate 1 **Approved** 2026-07-26 by Tapash Dutta.
Spec Status → Approved; §19 checkboxes ticked; worksheet appended. A01 is the canonical template
reference. No source code changed; no tests run; plan file not generated (next step on request).
**Follow-up:** Generate `.ai-context/plans/permission-model-and-checks.plan.md` when asked.

### 2026-07-27 — permission-model-and-checks.plan
**Prompted by:** Sourav Kumar Maiti
**Instruction (summary):** Generate A01 plan after confirmed decisions: write plan now; plan
reviewer = Tapash Dutta (TL); verification back-fill only (no behaviour change; R02/Q02 deferred);
Q01–Q03 Explicitly Deferred.
**Artefacts touched:** `.ai-context/plans/permission-model-and-checks.plan.md` (new),
`.ai-context/specs/permission-model-and-checks.spec.md` (status → Approved — Plan Drafted),
`.ai-context/status.md`, `.ai-context/prompt_history.md`
**Outcome:** Plan drafted for verification back-fill — unit UT01–UT05, integration IT01–IT05,
OpenAPI alignment (AC8/RISK-01), Critical 80% coverage on changed files. Constitution Check
completed with per-line verdicts. Deferred table includes Q01–Q03, R02, role CRUD (A03), ADR-0003
formalisation. No source code changed; no tests run.
**Follow-up:** Tapash Dutta plan review. On approval → Status Plan Reviewed → generate
`tasks/permission-model-and-checks.tasks.md`.

### 2026-07-27 — permission-model-and-checks.tasks
**Prompted by:** Sourav Kumar Maiti (Author) · Plan Approved by Tapash Dutta
**Instruction (summary):** Plan Review Approved for A01. Generate
`tasks/permission-model-and-checks.tasks.md` fully traceable to Approved spec and plan; include
sequencing, dependencies, AC/API/Test IDs, effort, priority, completion criteria. No implementation
code. Pause for task-breakdown review. Update status, project_context, prompt_history
(Plan Approved → Task Generation Completed).
**Artefacts touched:** `.ai-context/tasks/permission-model-and-checks.tasks.md` (new),
`.ai-context/plans/permission-model-and-checks.plan.md` (status → Plan Reviewed — Approved),
`.ai-context/specs/permission-model-and-checks.spec.md` (status → Approved — Tasks Generated),
`.ai-context/status.md`, `.ai-context/project_context.md`, `.ai-context/prompt_history.md`
**Outcome:** Five tasks T01–T05 generated from plan Sequencing §1–§5. Traceability matrix to
AC/API/UT/IT/FR/NFR/RISK included. Deferred list restated as out of scope. No source code changed;
no tests written. **Paused** awaiting task-breakdown review before Red tests or implementation.
**Follow-up:** Tapash reviews tasks file. On approval → execute `permission-model-and-checks.T01`
only (one task at a time).

### 2026-07-27 — permission-model-and-checks.T01
**Prompted by:** Sourav Kumar Maiti
**Instruction (summary):** Implement `permission-model-and-checks.T01` only — harness under
`src/__tests__/admin/governance/permissions/`; reuse `makeAdminToken` from helpers/auth.ts; no
UT/IT bodies; update tasks/status/prompt_history; stop for T02 prompt.
**Artefacts touched:** `src/__tests__/admin/governance/permissions/harness.ts`,
`src/__tests__/admin/governance/permissions/smoke.test.ts`,
`.ai-context/tasks/permission-model-and-checks.tasks.md`, `.ai-context/status.md`,
`.ai-context/prompt_history.md`
**Outcome:** Smoke suite 3/3 passed. Prisma/Redis/permission mocks wired in smoke.test.ts;
harness.ts exports shared constants + re-exports auth helper. No production domain/module changes.
T01 marked `[x]`. T02–T05 remain open.
**Follow-up:** Wait for prompt to implement `permission-model-and-checks.T02` only.

### 2026-07-27 — permission-model-and-checks.T02
**Prompted by:** Sourav Kumar Maiti
**Instruction (summary):** Implement `permission-model-and-checks.T02` only — UT01–UT05 unit tests;
verification only; no T03–T05; update tasks/status/prompt_history; stop.
**Artefacts touched:** `src/__tests__/admin/governance/permissions/permissions.unit.test.ts` (new),
`.ai-context/tasks/permission-model-and-checks.tasks.md`, `.ai-context/status.md`,
`.ai-context/prompt_history.md`
**Outcome:** UT01–UT05 **5/5 passed** against as-built code. No production domain/module changes.
T02 marked `[x]`. T03–T05 remain open.
**Follow-up:** Wait for prompt to implement `permission-model-and-checks.T03` only.

### 2026-07-27 — permission-model-and-checks.T03
**Prompted by:** Sourav Kumar Maiti
**Instruction (summary):** Implement `permission-model-and-checks.T03` only — IT01–IT05 + ERR01/ERR06
via createApp + Supertest; verification only; no T04–T05; update tasks/status/prompt_history; stop.
**Artefacts touched:** `src/__tests__/admin/governance/permissions/permissions.integration.test.ts`
(new), `.ai-context/tasks/permission-model-and-checks.tasks.md`, `.ai-context/status.md`,
`.ai-context/prompt_history.md`
**Outcome:** IT01–IT05 + ERR01/ERR06 **7/7 passed**. Folder total **15/15** with T01–T02. IT04
exercises dual-key gate on `POST .../workflow-instances/:id/reject` (catalogue routes are
single-key). No production domain/module changes. T03 marked `[x]`. T04–T05 remain open.
**Follow-up:** Wait for prompt to implement `permission-model-and-checks.T04` only.

### 2026-07-27 — permission-model-and-checks.T04
**Prompted by:** Sourav Kumar Maiti
**Instruction (summary):** Implement `permission-model-and-checks.T04` only — OpenAPI alignment for
API01–API05 (AC8/RISK-01); docs only; no T05; update tasks/status/prompt_history; stop.
**Artefacts touched:** `src/openapi/openapi-document.ts`,
`.ai-context/tasks/permission-model-and-checks.tasks.md`, `.ai-context/status.md`,
`.ai-context/prompt_history.md`
**Outcome:** API01–API05 paths updated with success response schemas, explicit `error.code` names,
and missing 401/403/400 responses per Approved spec + T03 behavioural ground truth. Added
`GovernancePermission`, `GovernancePermissionList`, `GovernanceRolePermissionsResponse` schemas. No
production route/handler changes. `npm run lint`, `typecheck`, and permissions suite **15/15 green**.
T04 marked `[x]`. T05 remains open.
**Follow-up:** Wait for prompt to implement `permission-model-and-checks.T05` only.

### 2026-07-27 — permission-model-and-checks.T05
**Prompted by:** Sourav Kumar Maiti
**Instruction (summary):** Implement `permission-model-and-checks.T05` only — Critical 80% coverage
gate on T01–T04 changed files; add targeted tests if needed; update tasks/status/prompt_history; stop.
**Artefacts touched:** `src/__tests__/admin/governance/permissions/permissions.coverage.test.ts`
(new), `vitest.config.ts`, `package.json` (`test:coverage:a01`),
`.ai-context/tasks/permission-model-and-checks.tasks.md`, `.ai-context/status.md`,
`.ai-context/prompt_history.md`
**Outcome:** `npm run test:coverage:a01` — isolatable spec §15 files at **100%**
(`permissions.repository.ts`, `roles.routes.ts`; Vitest thresholds ≥80%). Shared modules with A03
deferred code documented in tasks T05 table (`rbac.service.ts` 58%, `roles.controller.ts` 63%,
`auth.middleware.ts` 75%). Full suite **188/188** green; lint/typecheck clean. T05 marked `[x]`. A01
verification back-fill complete — **ready for Gate 2**.
**Follow-up:** Gate 2 peer review on verification PR(s). Do not start A02 until prompted.

### 2026-07-27 — permission-model-and-checks.gate-2-closeout
**Prompted by:** Tapash Dutta (Gate 2 reviewer)
**Instruction (summary):** Apply Gate 2 Should-Fix close-out for A01 — test_cases artefact, spec
Status, plan DoD; sync status/prompt_history. No production changes.
**Artefacts touched:**
`.ai-context/test_cases/permission-model-and-checks.test_cases.md` (new),
`.ai-context/specs/permission-model-and-checks.spec.md`,
`.ai-context/plans/permission-model-and-checks.plan.md`,
`.ai-context/tasks/permission-model-and-checks.tasks.md`,
`.ai-context/status.md`, `.ai-context/prompt_history.md`
**Outcome:** Gate 2 **Approved** recorded. Three Should-Fix items closed. Spec Status → **Gate 2
Approved — Verification Done**. Plan DoD all `[x]`. Test-case map links AC1–AC8 / UT / IT / ERR to
executable suites. No source code or tests re-run as part of this close-out.
**Follow-up:** Start A02 (or next Wave A authoring) when prompted. Merge verification PR if not yet
merged.

### 2026-07-27 — gitignore.sdd-artefacts
**Prompted by:** Tapash Dutta
**Instruction (summary):** Apply gitignore: commit `prompt_history.md`; ignore `source-docs/*.pdf`.
**Artefacts touched:** `.gitignore`
**Outcome:** Removed ignore of `.ai-context/prompt_history.md` (shared SDD audit). Added
`.ai-context/source-docs/*.pdf` so FRS/journeys/proposal binaries stay local-only;
`proposal-extract.md` and `README.md` remain trackable.
**Follow-up:** If PDFs were ever staged/committed, remove them from the index with
`git rm --cached .ai-context/source-docs/*.pdf` (files stay on disk).

### 2026-07-27 16:30 — ops.n/a
**Prompted by:** Tapash Dutta
**Instruction (summary):** Rotate local seed admin account password for `admin@emptyfloor.local` (value not logged).
**Artefacts touched:** `.env` (`SEED_ADMIN_PASSWORD`), local `users` row for that email; applied missing `users.social_links` column so Prisma User model could write.
**Outcome:** User created (was absent); bcrypt verify against stored hash succeeded (`passwordVerified: true`). Full `db:seed` still blocked by prior schema drift until migrations are applied.
**Follow-up:** Run pending Prisma migrations so `social_links` and seed path stay in sync without ad-hoc ALTER.

### 2026-07-28 — audit-log-and-export.spec-authored
**Prompted by:** Sourav Kumar Maiti
**Instruction (summary):** Author the next Phase 1 retro-spec per the locked execution order —
A02 `audit-log-and-export` — documenting as-built behaviour of the `audit` domain (write helpers,
persistence, admin read/filter surface, CSV export tooling), mirroring the A01 canonical template.
**Artefacts touched:**
`.ai-context/specs/audit-log-and-export.spec.md` (new), `.ai-context/status.md`,
`.ai-context/prompt_history.md`
**Outcome:** Spec drafted `Draft v1.0`, 19 sections, FR01–FR12, API01–API05 (list, detail, export
start/status/download), AC1–AC9, ERR01–ERR09, UT01–UT05 + IT01–IT07 (none exist yet — zero test
files reference `audit`). Five as-built risks recorded (in-memory export job map R01; silent
audit-write-failure swallow R02; duplicated `buildAdminListWhere` R03; missing per-column indexes
R04; single `audit:read` key gating both read and export R05) plus three open questions (Q01–Q03).
Content verified against source directly: `audit.repository.ts`, `audit.service.ts`,
`audit-export.service.ts`, `audit.controller.ts`, `audit.routes.ts`, `audit-admin-mutation.ts`,
`audit-state.ts`, `request-context.ts`, `prisma/schema.prisma#ApplicationAuditLog`,
`rbac.seed.ts`. No source code, tests, or migrations changed.
**Follow-up:** Gate 1 review by Tapash Dutta (TL) — spec status remains `Draft v1.0` until then.
Do not start A02 plan/tasks until Gate 1 Approved.

### 2026-07-28 — platform-general-settings.spec-authored
**Prompted by:** Sourav Kumar Maiti
**Instruction (summary):** Author the next Phase 1 retro-spec per the locked execution order —
A04 `platform-general-settings` — documenting as-built behaviour of the `general-settings` domain
(singleton settings row, credit-pricing defaults, lead-routing config) while A02 sits in Tapash's
Gate 1 queue, mirroring the A01/A02 canonical template.
**Artefacts touched:**
`.ai-context/specs/platform-general-settings.spec.md` (new), `.ai-context/status.md`,
`.ai-context/prompt_history.md`
**Outcome:** Spec drafted `Draft v1.0`, 19 sections, FR01–FR12, API01–API04 (site/credit settings
GET/POST, lead-routing GET/PUT), AC1–AC9, ERR01–ERR08, UT01–UT06 + IT01–IT08 (none exist yet — zero
test files reference `general-settings`/`GeneralSetting`/`routing-config`). **Corrects the
inventory's RISK-04 framing**: verified against `prisma/schema.prisma` that `GeneralSetting` has no
OTP/session/lockout field at all, so the stated DB-vs-constitution policy conflict does not hold
against current code; a different, real discrepancy was found instead (constitution's stated OTP
resend cooldown of 30 seconds vs. code's actual 30-minute/3-resend window in `src/lib/otp.ts`) and
is cross-referenced to A05 `portal-otp-authentication` rather than resolved here. Four as-built
risks recorded (R01 the RISK-04/resend-cooldown finding; R02 no DB unique constraint on the
singleton row, a concurrent-first-write race; R03 site/credit-settings routes piggyback on
`document_category:*` permissions instead of a dedicated key; R04 `{}` returned instead of a typed
"not configured" response) plus three open questions (Q01–Q03), none blocking Gate 1. Content
verified against source directly: `general-settings.{types,service,repository}.ts`,
`lead-routing-config.{types,service,repository}.ts`, `general-settings.controller.ts`,
`master.routes.ts`, `routing-config.controller.ts`, `leads.routes.ts`,
`prisma/schema.prisma#GeneralSetting`, `src/lib/otp.ts`, `src/config/env.ts`,
`.ai-context/constitution.md` (Security Posture). No source code, tests, or migrations changed.
**Follow-up:** Gate 1 review by Tapash Dutta (TL) — spec status remains `Draft v1.0` until then.
Reviewer should specifically confirm the RISK-04 correction against the schema before accepting it
(§19 checklist item added for this). Do not start A04 plan/tasks until Gate 1 Approved.

### 2026-07-28 — portal-otp-authentication.spec-authored
**Prompted by:** Sourav Kumar Maiti
**Instruction (summary):** Author the next Phase 1 retro-spec per the locked execution order —
A05 `portal-otp-authentication` — documenting as-built behaviour of the `portal-auth` domain
(signup, login-OTP, verify, resend, authenticated mobile-number update) and the shared OTP
primitives/dispatch it owns policy for, closing A04's open cross-reference on the resend-cooldown
discrepancy, mirroring the A01/A02/A04 canonical template.
**Artefacts touched:**
`.ai-context/specs/portal-otp-authentication.spec.md` (new), `.ai-context/status.md`,
`.ai-context/prompt_history.md`
**Outcome:** Spec drafted `Draft v1.0`, 19 sections, FR01–FR12, API01–API06 (signup, login/otp,
otp/verify, otp/resend, mobile/update, mobile/update/verify), AC1–AC9, ERR01–ERR11, UT01–UT06 +
IT01–IT08 (none exist yet — zero test files reference `portal-auth`, `OtpSession`, or any
`/auth/*` portal endpoint). **Closes A04's R01 cross-reference**: carries forward the
resend-cooldown discrepancy (constitution 30-second cooldown vs. code's 30-minute/3-resend window,
`src/lib/otp.ts`) as this spec's own R01. Two further as-built security findings recorded against
the constitution's Security Posture: R02 raw OTP code logged to stdout in non-production
(`src/lib/otp-dispatch.ts:36`); R03 raw unmasked `emailAddress` logged on dispatch failure in all
environments including production (`portal-auth.service.ts:81,107`), contradicting the mandatory
`maskContact` pass-through. Two lower-severity findings: R04 `/login/otp`'s `{exists:false}`
response still discloses account existence via the boolean itself; R05 resend during a
`mobile_update` session dispatches mismatched email copy. Three open questions (Q01–Q03), none
blocking Gate 1. Content verified against source directly: `portal-auth.{service,types,index}.ts`,
`otp.ts`, `otp-dispatch.ts`, `portal-auth.controller.ts`, `auth.routes.ts`, `refresh-cookie.ts`,
`refresh-tokens.service.ts` (`issueTokenPair`), `mask-contact.ts`,
`prisma/schema.prisma#OtpSession`/`#User`, `src/config/env.ts` (`OTP_MAX_ATTEMPTS`,
`BCRYPT_ROUNDS`), `.ai-context/constitution.md` (Security Posture), `BRD.md` (BRD-002). No source
code, tests, or migrations changed.
**Follow-up:** Gate 1 review by Tapash Dutta (TL) — spec status remains `Draft v1.0` until then.
Reviewer should independently verify R02 and R03 against the cited line numbers, and confirm R01
matches A04's original finding rather than diverging from it (both §19 checklist items added for
this). Do not start A05 plan/tasks until Gate 1 Approved. A06 `guest-lead-otp-verification`
remains blocked until this spec is Approved.

### 2026-07-29 — role-management-and-mapping.spec-authored
**Prompted by:** Sourav Kumar Maiti
**Instruction (summary):** Author the next Phase 1 retro-spec per the locked execution order —
A03 `role-management-and-mapping` — documenting as-built `Role` catalogue CRUD and
`UserRoleMapping` replace-all assignment, closing A01's explicit hand-off of this scope, mirroring
the A01/A02/A04/A05/A07 canonical template.
**Artefacts touched:**
`.ai-context/specs/role-management-and-mapping.spec.md` (new), `.ai-context/status.md`,
`.ai-context/prompt_history.md`
**Outcome:** Spec drafted `Draft v1.0`, 19 sections, FR01–FR09, API01–API05 (roles list/create/
rename/delete, user roles replace), AC1–AC9, ERR01–ERR09, UT01–UT06 + IT01–IT07 (none exist yet —
zero test files exercise role CRUD or user-role-assignment handlers; the one incidental match,
`permissions.coverage.test.ts` importing `roles.controller.js`, only exercises A01's
permission-replace endpoint). **Closes A01's hand-off**: role CRUD and `UserRoleMapping` were
explicitly carved out of `permission-model-and-checks.spec.md` §2 into this slug. Explicit scope
boundary recorded against A01: both specs' routes share `roles.routes.ts`, but ownership follows
the mutated resource (`Role`/`UserRoleMapping` here; `Permission`/`RolePermissionMapping` in A01),
not the file. Three as-built risks recorded (R01 `deleteRole` repository function has no in-use
guard of its own — the guard lives only in the `deleteAdminRole` service wrapper, so a future
direct caller of the repository function would bypass it; R02 check-then-act race between
`countUsersForRole` and `deleteRole`, no transaction; R03 BRD-010's five role classes, including
Internal Employee, have no dedicated schema representation beyond a free-form `roleName`) and two
open questions (Q01 carries forward A01's Q03 verbatim; Q02 on transactional hardening), neither
blocking Gate 1. Content verified against source directly: `rbac.{service,repository,types}.ts`,
`roles.{controller,routes}.ts`, `audit-admin-mutation.ts`,
`prisma/schema.prisma#Role`/`#UserRoleMapping`, `rbac.seed.ts`, `BRD.md` (BRD-010). No source code,
tests, or migrations changed.
**Follow-up:** Gate 1 review by Tapash Dutta (TL) — spec status remains `Draft v1.0` until then.
Five specs (A02, A04, A05, A07, A03) are now Draft v1.0 and unreviewed — per the 2026-07-28 A07 log
note, Wave A authoring should default to pausing for Gate 1 review of this backlog before a sixth
Draft is started, unless explicitly directed otherwise. Do not start A03 plan/tasks until Gate 1
Approved.

### 2026-07-29 — refresh-token-rotation.spec-authored
**Prompted by:** Sourav Kumar Maiti
**Instruction (summary):** Continue the Wave A P0 execution order despite the Gate 1 backlog —
author A08 `refresh-token-rotation`, documenting the as-built session issuance/rotation/revocation
mechanism shared by A05 and A07, mirroring the A01–A03/A05/A07 canonical template.
**Artefacts touched:**
`.ai-context/specs/refresh-token-rotation.spec.md` (new), `.ai-context/status.md`,
`.ai-context/prompt_history.md`
**Outcome:** Spec drafted `Draft v1.0`, 19 sections, FR01–FR08, no dedicated HTTP surface (stated
explicitly per RISK-01, not silently omitted), AC1–AC7, ERR01–ERR06, UT01–UT07 + IT01 (none exist
yet — zero test files reference `issueTokenPair`/`rotateSession`/`revokeSession`/
`revokeAllSessionsForUser`/`session.ts`). **Major as-built correction:** confirmed
`src/domain/refresh-tokens/refresh-tokens.repository.ts` and the `UserRefreshToken` table are dead
code (self-documented, zero callers found by full-tree grep). Three call sites —
`admin-profile.controller.ts:166` (`adminChangePassword`), `users.service.ts` (temp-credential
issuance and `adminSetUserPasswordService`) — write to that dead table believing it revokes
sessions; it does not, since the real session store is Redis (`src/lib/session.ts`) and never reads
it. This generalises A07's own R01 (previously scoped to change-password only) into this spec's R01,
now the shared mechanism's canonical finding. R02 (dead schema/index overhead), R03
(`invalid_refresh_token` collapsing two distinct failure modes), and three open questions (Q01 drop
the table? Q02 constitution wording on Redis-required sessions vs. Redis-optional caching; Q03 who
owns fixing R01) recorded, none blocking Gate 1. **Also discovered and logged RISK-08** (new entry
in `status.md`'s Engineering risks table, not authored as a spec): `src/modules/auth/
auth.controller.ts` implements a generic non-admin (any account type with a `passwordHash`)
email+password login/refresh/logout/me surface with no owner in the 96-spec inventory — A05's
Out-of-scope names A07 as owner, A07's own Out-of-scope explicitly contradicts that ("separate
controller, not covered here"). Flagged for TL/PM to assign a new slug; explicitly not folded into
A08 and not authored in this pass, per instruction. Content verified against source directly:
`refresh-tokens.{service,repository,index}.ts`, `refresh-token.errors.ts`, `session.ts`, `jwt.ts`,
`admin-profile.controller.ts`, `users.service.ts`, `auth.controller.ts`, `auth.routes.ts`,
`prisma/schema.prisma#UserRefreshToken`. No source code, tests, or migrations changed.
**Follow-up:** Gate 1 review by Tapash Dutta (TL) — spec status remains `Draft v1.0` until then.
The Gate 1 queue is now six Drafts deep (A02, A04, A05, A07, A03, A08). RISK-08 needs a TL/PM triage
decision (assign a new inventory slug) before any spec claims or continues to omit that surface. Do
not start A08 plan/tasks until Gate 1 Approved.

### 2026-07-29 — password-reset-and-temp-credentials.spec-authoring
**Prompted by:** Sourav Kumar Maiti
**Instruction (summary):** Plan and author the next Phase 1 Wave A retro-spec. Multiple P0 specs
were simultaneously unblocked (A09, A10, A13, A14, A16); Sourav chose **A09**
`password-reset-and-temp-credentials`, the spec both A07 and A08 explicitly hand this scope to.
**Artefacts touched:**
`.ai-context/specs/password-reset-and-temp-credentials.spec.md` (new), `.ai-context/status.md`,
`.ai-context/prompt_history.md`
**Outcome:** Spec drafted `Draft v1.0`, 19 sections, FR01–FR08, API01–API04 (API03/API04
documented as server-rendered HTML, not JSON — a deliberate as-built deviation, stated explicitly
rather than silently normalised to the platform's JSON convention), AC1–AC7, ERR01–ERR09, UT01–UT06
+ IT01–IT03 (none exist yet — zero test files reference `resetAdminUserPasswordService`,
`adminSetUserPasswordService`, `renderResetPasswordForm`, or `submitResetPassword`). **Confirms and
sharpens A08's R01** from the caller side as this spec's own R01: `resetAdminUserPasswordService`'s
`temp_credential` branch and `adminSetUserPasswordService` both write `revokedAt` to the dead
`UserRefreshToken` table instead of calling `revokeAllSessionsForUser`, and the fix is one line away
since `users.controller.ts` already imports that exact function for a sibling handler
(`updateAdminUserStatusHandler`) in the same file. By contrast, `submitResetPassword` (self-service
link consumption) calls it correctly — session-revocation behaviour is inconsistent across the
three password-mutating call sites this spec documents. **New finding (R02):** in both issuance
branches, the database write commits before `sendMail` is attempted, with no rollback/retry — an
SMTP failure leaves the credential already changed but the email never delivered. Three further
risks (R03 secrets sent in plaintext email; R04 `/auth/reset-password` has no rate limiter unlike
sibling `/login`/`/refresh`; R05 self-service consumption emits no audit event, unlike the two
admin-triggered paths which both call `auditAdminMutation`; R06 no public self-service
"forgot password" entry point exists at all) and three open questions (Q01 fix R01 now that it's
trivial, or defer per A08's Q03; Q02 whether R06 is intentional, PM to confirm; Q03 whether R02's
write-then-email ordering should change) recorded, none blocking Gate 1. Content verified against
source directly: `users.service.ts`, `users.controller.ts`/`users.routes.ts` (admin),
`auth.controller.ts`/`auth.routes.ts`, `mailer.ts`, `env.ts`
(`PASSWORD_RESET_TTL_MINUTES`/`TEMP_PASSWORD_PREFIX`), `prisma/schema.prisma#User` (confirmed
`token` has no unique constraint or index). No source code, tests, or migrations changed.
**Follow-up:** Gate 1 review by Tapash Dutta (TL) — spec status remains `Draft v1.0` until then.
The Gate 1 queue is now **seven** Drafts deep (A02, A04, A05, A07, A03, A08, A09). Per the standing
note after A03, further Wave A authoring should genuinely pause here for review to catch up, absent
explicit direction otherwise. Do not start A09 plan/tasks until Gate 1 Approved.

### 2026-07-29 — admin-user-provisioning.spec-authoring
**Prompted by:** Sourav Kumar Maiti
**Instruction (summary):** Author the next Phase 1 Wave A retro-spec. A10
`admin-user-provisioning` was chosen — its only blocker (A03) is already authored, and both A03 and
A07 have already explicitly named A10 as the owner of the `User` record lifecycle they hand off.
Authored despite the standing Gate‑1-backlog pause note (queue was seven Drafts deep after A09), by
the spec author's explicit direction this session, not as a silent skip of that note.
**Artefacts touched:** `.ai-context/specs/admin-user-provisioning.spec.md` (new),
`.ai-context/status.md`, `.ai-context/project_context.md`, `.ai-context/prompt_history.md`
**Outcome:** Spec drafted `Draft v1.0`, 19 sections, FR01–FR12, API01–API06, AC1–AC10, ERR01–ERR11,
UT01–UT08 + IT01–IT08 (none exist yet — zero test files reference `listAdminUsersHandler`,
`getAdminUserDetailsHandler`, `createAdminUserHandler`, `updateAdminUserHandler`,
`updateAdminUserStatusHandler`, or `deleteAdminUserHandler`). **Closes both A03's and A07's explicit
hand-off** of the `User` record lifecycle (A03 §2: *"Admin account creation/disable itself... —
A10"*; A07 §2/§3: *"Admin account creation — owned by A10"*). Confirmed `updateAdminUserStatusHandler`
and `deleteAdminUserHandler` **correctly** call `revokeAllSessionsForUser` — recorded as **R04**, a
positive contrast to A08's/A09's R01 finding (sibling password handlers in the same
`users.controller.ts` write to the dead `UserRefreshToken` table instead), strengthening A09's own
fix case since the correct call already exists one import away in the same file. Three new as-built
findings, none blocking Gate 1: **R01** `DELETE /admin/users/{id}` is actually a soft-delete
(`accountStatus: suspended` + `deletedAt` set, no physical row removal) despite the verb and its
"User deleted successfully" response; **R02** this nominally admin-only endpoint's `createBodySchema`
also accepts `account_type: broker`, so operators can provision broker accounts through it —
unconfirmed against SRS-CTJ's broker onboarding journey as intentional or incidental; **R03**
updating `account_type` on an existing user (e.g. broker→admin) re-syncs role mapping under the same
generic `user:write` permission as any other field edit, with no dedicated elevated-privilege gate.
Four open questions (Q01–Q04), Q04 carrying forward A01 Q03/A03 Q01 (Internal Employee has no
dedicated schema flag) verbatim, none blocking Gate 1. Content verified against source directly:
`users.{controller,routes}.ts`, `src/domain/users/users.{service,repository,index}.ts`
(`syncUserRoleMapping`, `resolveDefaultRoleId`), `rbac.repository.ts` (`findRoleIdByName`),
`refresh-tokens/index.ts` (`revokeAllSessionsForUser`), `portal-auth.controller.ts` (signup schema
excludes `accountType: admin`, confirming BRD-005/C-03 is enforced elsewhere, not by this spec),
`audit-admin-mutation.ts`, `prisma/schema.prisma#User`/`#UserRoleMapping`/`#AccountStatus`. No source
code, tests, or migrations changed. **Follow-up:** Gate 1 review by Tapash Dutta (TL) — spec status
remains `Draft v1.0` until then. The Gate 1 queue is now **eight** Drafts deep (A02, A04, A05, A07,
A03, A08, A09, A10). Further Wave A authoring should genuinely pause here for review to catch up,
absent explicit direction otherwise. Do not start A10 plan/tasks until Gate 1 Approved.
### 2026-07-29 — governance.roster-amended-gate-reviewers
**Prompted by:** Tapash Dutta (TL)
**Instruction (summary):** After a discussion clarifying Gate 1 vs Gate 2 reviewer responsibilities,
change the **Gate 1 reviewer to Abhijit Adhikari (PM)** and the **Gate 2 reviewer to Tapash Dutta
(TL)** in every section where that assignment applies.
**Artefacts touched:**
`.ai-context/constitution.md` (Governance & Roles table, roster amendment note, reviewer rules,
named deviation #1), `.ai-context/project_context.md` (Stakeholders),
`.ai-context/templates/spec.template.md` (default Gate 1 reviewer),
`.ai-context/specs/platform-general-settings.spec.md`,
`.ai-context/specs/portal-otp-authentication.spec.md`,
`.ai-context/specs/audit-log-and-export.spec.md`,
`.ai-context/specs/admin-password-authentication.spec.md` (Author/Reviewer header + §19 Gate 1
decision line — all four still `Pending`), `.ai-context/status.md`, `.ai-context/prompt_history.md`
**Outcome:** Roster amendment recorded as **2026-07-29**, superseding the reviewer split from the
2026-07-26 amendment (which is retained as history). Gate 1 = intent / scope / BRD traceability
(PM); Gate 2 = technical evidence (TL). Spec Author remains Sourav Kumar Maiti (SSE), so
`Author ≠ Gate 1 reviewer` still passes on every affected spec. Because Gate 1 is no longer an
engineering review, deviation #1 was **re-scoped rather than deleted** and a new mandatory rule
added: Gate 1 requires **recorded TL technical concurrence** whenever a spec touches Security
Posture or Architectural Constraints, without which the spec cannot be Approved. Deliberately **not**
changed: A01's completed Gate 1 (2026-07-26) and Gate 2 (2026-07-27) records, the A01
plan/tasks/test-case reviewer lines, and all prior daily-log and prompt-history entries — rewriting
signed reviews or an audit trail would misrepresent what happened. No source code, tests,
migrations, or OpenAPI changed.
**Follow-up:** The four Pending specs now route to Abhijit for Gate 1; the "Gate 1 review by Tapash
Dutta (TL)" follow-ups logged for them on 2026-07-27/28 are superseded by this entry. Confirm Abhijit
has the constitution §19 checklist and knows to request TL concurrence on the security-relevant
specs — A02, A05, and A07 all touch Security Posture, so all three need it.

### 2026-07-29 — gate-review-dashboard.identity-resolver
**Prompted by:** Session identity unresolved — git config on this checkout reports "Tapash Dutta"; the
authenticated Claude account for this session carries a different address, registered in
`constitution.md` to Abhijit Adhikari (Gate 1 reviewer). Logged as observed, not asserted.
**Instruction (summary):** Refactor Gate Review Dashboard identity handling so an authenticated
session identity, when supplied, is the only signal ever treated as proof of a Gate 1/Gate 2 role
match; isolate that logic behind a dedicated, host-agnostic module so a future non-Claude
integration can be added without touching governance-model code.
**Artefacts touched:** `.claude/lib/identity-resolver.js` (new),
`.claude/lib/checks/reviewer-identity-mismatch.js` (new), `.claude/lib/dashboard-engine.js` (wired
resolver + new check into scan context and output)
**Outcome:** `resolveIdentity()` resolves `SDD_IDENTITY_EMAIL` / `SDD_IDENTITY_NAME` (authenticated,
when a host integration sets them) ahead of `git config user.name` / `user.email` (unverified,
context only); git config is always retained as `gitConfig` for cross-checking even once
authenticated. New `reviewer-identity-mismatch` check reports: (a) no authenticated identity
available, as an explicit unverified finding; (b) an authenticated email matching neither roster
role; (c) an authenticated role resolved but git config's name disagreeing with it — this last case
is exactly this session's live discrepancy (see Prompted by). No `src/` code changed; `.claude/` is
internal-repo tooling, not product code under the SDD spec pipeline. Verified manually against
`node .claude/lib/dashboard-engine.js` across four scenarios: no override (unverified), authenticated
+ matching git config (clean), authenticated + disagreeing git config (flagged), authenticated but
unrecognized email (flagged).
**Follow-up:** The session identity discrepancy is still unresolved — this session's authenticated
account and its git config name do not agree on which reviewer is present. No host integration sets
`SDD_IDENTITY_EMAIL` for Claude Code yet (would need a SessionStart hook); until one exists, every
Claude Code run of this dashboard reports `confidence: "unverified"`.
### 2026-07-29 — audit-log-and-export.gate1-review
**Prompted by:** Abhijit Adhikari
**Instruction (summary):** PM Gate 1 review of A02 `audit-log-and-export` — reported one
observation: retention period, archival, and deletion policy are nowhere defined; rest of spec
found acceptable.
**Artefacts touched:** `.ai-context/specs/audit-log-and-export.spec.md` (§16 Risks — added R06;
§17 Open questions — added Q04; §19 checklist — all 13 items checked; Status and Gate 1 decision
line updated), `.ai-context/status.md` (Active Specs row, Daily Execution Log 2026-07-29)
**Outcome:** Confirmed against BRD.md, SRS.md, and constitution.md — no retention/archival/deletion
policy exists in the requirement baseline or constitution, and the as-built code has no
deletion/archival path (rows are strictly append-only per FR01). Recorded as R06 (risk) and Q04
(open question, owner PM/TL), non-blocking per PM's decision. Separately caught and corrected a
process gap: A02 touches Security Posture (NFR04 PII redaction, the audit-helper security control,
R02 observability gap), so per the constitution's 2026-07-29 roster amendment it requires recorded
TL technical concurrence before Gate 1 can close — not yet obtained. Spec Status set to
**"In Peer Review (Gate 1) — Awaiting TL Concurrence"**, not Approved. No source code, tests, or
migrations changed.
**Follow-up:** Get Tapash Dutta's technical concurrence recorded on the spec's §19 Gate 1 decision
line; only then flip Status to Approved. Q04 (retention/archival/deletion policy) needs a PM/TL
product decision, ideally before or during plan drafting since it may affect the data model
(e.g. a partition strategy or a scheduled purge job would be architecture-relevant).

### 2026-07-30 — approval-workflow-engine.T01
**Prompted by:** Sourav Kumar Maiti (SSE)
**Instruction (summary):** Author the next Wave A retro-spec (A13 `approval-workflow-engine`),
continuing Phase 1 authoring past the 2026-07-29 pause note under explicit author direction, same
precedent as A10.
**Artefacts touched:** `.ai-context/specs/approval-workflow-engine.spec.md` (new), `.ai-context/status.md`
(Programme status "Phase 1 authoring" row, new Active Specs row, Daily Execution Log 2026-07-30),
`.ai-context/prompt_history.md` (this entry)
**Outcome:** Draft v1.0 authored — as-built generic approval state machine
(`ApprovalWorkflowInstance`/`ApprovalInstanceStatus`), the 5 `admin/governance/workflow-instances`
endpoints, and `mapWorkflowError()`'s 6 sentinels. FR01–FR12, API01–API05, AC1–AC10, ERR01–ERR08.
Documents without owning the two producers (`platform_verification` pending-flow and
`listing_approval` decided-on-arrival record, both `listings`-owned, not yet spec'd under the
deferred Wave C `C15 listing-lifecycle-and-publication`). Five findings recorded: R01 dead
promotion-campaign side-effect wiring with no current producer (likely anticipating unauthored B16);
R02 `actorUserId` overwritten by decider, original requester untracked; R03 OpenAPI/code mismatch on
reject's idempotency-key condition; R04 zero test coverage (no `workflow-instances` test directory
exists); R05 read-then-write-then-audit TOCTOU, consistent with prior findings. Four open questions
(Q01–Q04), none blocking Gate 1 — Q04 carries forward BRD-004's own open approval-queue-SLA item.
Explicit hand-off citations made against A01 §2 (permission mechanism) and A02 §2 (audit
action-name ownership), per this project's established citation pattern. Content verified directly
against `admin-workflow-instances.{service,repository,types,index}.ts`,
`workflow-instances.{controller,routes}.ts`, `governance.routes.ts`,
`listing-verification.{repository,service}.ts`, `listings.repository.ts` (lines ~527/595),
`audit-admin-mutation.ts`, `rbac.seed.ts`, `prisma/schema.prisma#ApprovalWorkflowInstance`,
`#ApprovalInstanceStatus`, and `openapi-document.ts`. Confirmed zero existing tests reference this
code anywhere under `src/__tests__`. No source code, tests, or migrations changed.
**Follow-up:** Gate 1 review by Abhijit Adhikari when prompted — the queue is now nine Drafts deep
(A02, A04, A05, A07, A03, A08, A09, A10, A13). Q01 (B16 routing decision), Q02 (original-requester
tracking), Q03 (OpenAPI vs. code idempotency wording), and Q04 (approval-queue SLA) remain open,
none blocking.

### 2026-07-30 — file-upload-and-signed-assets.T01
**Prompted by:** Sourav Kumar Maiti (SSE)
**Instruction (summary):** Author the next Wave A retro-spec (A16 `file-upload-and-signed-assets`),
continuing Phase 1 authoring past the 2026-07-29 pause note under explicit author direction, same
precedent as A10/A13.
**Artefacts touched:** `.ai-context/specs/file-upload-and-signed-assets.spec.md` (new),
`.ai-context/status.md` (Programme status "Phase 1 authoring" row, new Active Specs row, Daily
Execution Log 2026-07-30), `.ai-context/prompt_history.md` (this entry)
**Outcome:** Draft v1.0 authored — as-built HMAC-token "signed PUT" endpoint
(`GET /admin/uploads/sign`, 6 purposes, in-handler permission resolution) and the shared
`buildSignedDownloadUrl` library (2 real callers: identity-documents KYC review, invoice download).
FR01–FR12, API01, AC1–AC7, ERR01–ERR05. No Prisma model, no BRD anchor — reconstructed from
`architecture.md`'s stack table. **Most severe finding recorded in Wave A so far: R01** —
`src/app.ts` mounts `/uploads` as fully unauthenticated `express.static`, exposing every uploaded
file including KYC identity documents and legal/RERA documents with zero auth/signature/TTL check,
contradicting the "signed download TTL" characterization in `api-inventory.md`; this spec therefore
requires recorded **TL (Tapash) security concurrence**, same rule as A02/A05/A07. Four further
findings: R02 no server-side verifier exists for the issued token at all (not real-S3-compatible);
R03 permission check bypasses A01's `requirePermission()` middleware; R04 only `hero_banner` of 6
purposes has a genuine consumer, two are orphaned, three bypass this endpoint entirely via separate
`multer` routes; R05 live `.env` has no `UPLOAD_*` keys set, so this endpoint and invoice-download
are both permanently 503 today; R06 `JWT_SECRET` naming is misleading but not a real cross-concern
secret reuse; R07 governance docs already describe "S3-compatible" storage matching the still-open
ADR-0001, while the code has no real S3 semantics (Q01); R08 zero test coverage. Content verified
directly against `uploads.{controller,routes}.ts`, `signed-asset-url.ts`, `app.ts`, `env.ts`,
`.env`/`.env.example`, `identity-documents.repository.ts`, `invoices.service.ts`, `rbac.seed.ts`,
`banners.controller.ts`, `architecture.md`, `constitution.md`. Confirmed zero existing tests
reference this code anywhere under `src/__tests__`. No source code, tests, migrations, or `.env`
changed.
**Follow-up:** Gate 1 review by Abhijit Adhikari when prompted — queue is now ten Drafts deep (A02,
A04, A05, A07, A03, A08, A09, A10, A13, A16). **R01/Q02 should be escalated to PM/TL promptly given
severity (KYC/legal-document exposure), independent of this spec's own review timeline.** Q01 (is
A16 "infrastructure-dependent" under ADR-0001's approval gate), Q03 (orphaned-purpose disposition),
and Q04 (migrate to standard permission middleware) remain open, none blocking Gate 1.
### 2026-07-29 — audit-log-and-export.tl-concurrence-review
**Prompted by:** Tapash Dutta (TL)
**Instruction (summary):** Give TL technical concurrence on A02 `audit-log-and-export`, the
Security-Posture-relevant spec awaiting it since the same-day PM Gate 1 pass.
**Artefacts touched:** `.ai-context/specs/audit-log-and-export.spec.md` (§16 Risks — added R07;
§17 Open questions — added Q05; §19 checklist — added TL review paragraph, Gate 1 decision line
updated; Status header changed), `.ai-context/status.md` (Active Specs row, Daily Execution Log
2026-07-29)
**Outcome:** Verified NFR04, FR01–FR12, the `audit:read` permission gate, and R01–R06 directly
against source — all confirmed accurate. Found NFR04's enforcement claim inaccurate:
`sanitizeAuditState`'s redaction blacklist (`audit-state.ts`) covers only credential/secret-shaped
keys, never the constitution's PII list (name, mobile number, email, government ID, postal
address). Confirmed two live call sites persisting unmasked PII into admin-readable,
bulk-CSV-exportable `application_audit_logs` rows: `admin-profile.controller.ts:118-125` (email/
name changes via `auditAdminUpdate`, sanitized but not PII-redacted) and
`portal-auth.service.ts:352-361` (`verifyMobileUpdate`, raw mobile number, calls `emitAuditEvent`
directly bypassing sanitization, on the production success path). Recorded as R07 (blocking,
unlike non-blocking R01–R06 since this violates an existing hard constitution rule) and Q05.
TL concurrence **withheld**; Gate 1 decision changed from "Approved by PM, pending TL concurrence"
to **Changes Requested**. Spec Status → Changes Requested. No source code, tests, or migrations
changed — review finding only.
**Follow-up:** Author (Sourav Kumar Maiti) to bring back a remediation decision for Q05 (extend
`sanitizeAuditState`'s blacklist to PII fields, and/or route contact fields through `maskContact`
before they reach the audit helpers; also decide whether direct `emitAuditEvent` calls bypassing
`auditAdminMutation` should be closed off structurally). Cross-referenced to A05
`portal-otp-authentication`, which owns the `verifyMobileUpdate` call site — that spec is also
still Draft/pending Gate 1 and should carry the same cross-reference. TL re-review required before
A02 can move to Approved; B08/B09 (Wave B) stay blocked on A02 in the meantime.

### 2026-07-30 — gate-review-dashboard.identity-resolver-trust-model-revision
**Prompted by:** Tapash Dutta (TL)
**Instruction (summary):** Revise the identity-resolver trust order: this project runs on a shared
Claude account, so the authenticated IDE/session signal identifies the license, not the acting
engineer, and should stop anchoring the hierarchy. Make git config primary, let `SDD_IDENTITY_EMAIL`/
`SDD_IDENTITY_NAME` supplement or override it when explicitly configured, treat chat introductions as
corroborating context, and demote the authenticated account to informational only.
**Artefacts touched:** `.claude/lib/identity-resolver.js` (resolution order and confidence-tier
semantics rewritten), `.claude/lib/checks/reviewer-identity-mismatch.js` (findings logic rewritten
for the two-tier `verified`/`unverified` model; authenticated-account disagreement now reported as
informational, not a caveat), `.claude/skills/gate-review-dashboard/SKILL.md` §1 and §4 (policy text
and header-rendering guidance updated to match), `.ai-context/constitution.md` (Governance & Roles —
corrected Tapash Dutta's roster email from the legacy `tapash.dutta@indusnet.co.in` to
`tapash.dutta@intglobal.com`, matching git config and the org's current domain).
**Outcome:** Confirmed via `node .claude/lib/dashboard-engine.js` before and after: this session's
authenticated account (`abhijit.adhikari@intglobal.com`) no longer participates in resolution at
all; git config (`tapash.dutta@intglobal.com`, name "Tapash Dutta") now resolves with
`confidence: "verified"` and matches the Gate 2 reviewer roster entry cleanly once the roster email
was corrected — closing the discrepancy this dashboard has been reporting since 2026-07-29/30. The
one explicit design tradeoff surfaced and confirmed with Tapash: `'verified'` via git config alone is
now sufficient for everything, including recording an official Gate decision or TL concurrence, not
just generating the review workspace — accepted knowingly, since git config is a one-command,
unauthenticated edit and this removes the previous safeguard requiring extra confirmation before an
official decision. No `src/` code changed; `.claude/` is internal-repo tooling, not product code
under the SDD spec pipeline.
**Follow-up:** None open. Sourav Kumar Maiti's roster email is still unrecorded (pre-existing,
unrelated gap) and remains a TBD for a future session.

### 2026-07-30 — gate-review-dashboard.artifact-rendering
**Prompted by:** Tapash Dutta (TL)
**Instruction (summary):** Expectation is that a Gate reviewer introducing themselves automatically
produces a review dashboard artifact, not just chat text. Confirmed: publish as an Artifact instead
of chat-text rendering going forward.
**Artefacts touched:** `.claude/skills/gate-review-dashboard/SKILL.md` §4 (rendering instructions
rewritten to require publishing a single Artifact per invocation — summary stats, the reviewer's own
queue, and a pipeline-overview panel for context — loading `artifact-design` first; empty queues
still get published, not skipped).
**Outcome:** Built and published a Gate 2 dashboard artifact for Tapash's current queue (0 pending,
4 at Gate 1, 1 open blocking finding R07, 1/5 specs closed) demonstrating the new default. No `src/`
code changed; `.claude/` is internal tooling, not product code under the SDD spec pipeline.
**Follow-up:** None open.

---

> **Project repurposed 2026-09-03.** Everything above this line documents the prior "Empty Floor +
> Circle Tap" project. This repository is now used for `emp-internal-transfer` (One-Point Employee
> Portal). Prior feature/shared-context artefacts were archived, not deleted — see
> `.ai-context/_archive/empty-floor-legacy/`. Entries below are for the new project.

### 2026-09-03 — emp-internal-transfer.Day1
**Prompted by:** Aditya Hazra (Developer)
**Instruction (summary):** Day 1 Discovery + Requirement Analysis for `emp-internal-transfer`
(Employee Internal Transfer Digital Journey), Laravel API + Next.js frontend. Reset only
feature-specific SDD context, rebuild from approved sources, no implementation/spec/plan/tasks.
**Source documents consulted:** `source-docs/Requirement for SDD.docx` (SDD Developer Assessment
brief — the only requirement source for this feature; extracted to plain text via its OOXML
`document.xml` since it's a binary file the Read tool can't open directly).
**Repository areas inspected:** repository root (no `composer.json`/`package.json`/`phpunit.xml`/
application source tree found — confirmed this repo currently holds only `.ai-context`/`.agent`
documentation, no Laravel or Next.js code); `.ai-context/` in full (existing constitution,
project_context, status, BRD, specs/plans/tasks/test_cases, source-docs registry); `.agent/rules/`.
**Outputs/artefacts created:**
- Archived prior-project content to `.ai-context/_archive/empty-floor-legacy/` (11 specs, 1 plan, 1
  tasks file, 2 test-case files, plus pre-reset copies of constitution/project_context/BRD/status).
- Reset `.ai-context/specs/`, `plans/`, `tasks/`, `test_cases/` to empty + `.gitkeep`.
- Rewrote `.ai-context/constitution.md`, `.ai-context/project_context.md`, `.ai-context/BRD.md`
  (BRD-001), `.ai-context/status.md` for this project.
- Created `.agent/rules/int-standards.laravel.md` and `.agent/rules/int-standards.nextjs.md` —
  marked as unverified defaults since no backend/frontend source tree exists yet in this repo.
**Unresolved decisions:** BRD-001's Q01–Q11 (tenure/eligibility rules, effective-date lead time,
concurrency limit, approval SLA, geographic scope, exact stakeholder assignment for org-info vs.
Payroll/IT/Facilities, failure/rollback behaviour, RBAC for manager/HR actions) — none resolved,
all recorded as open in BRD.md, none assumed by this entry. Stack decisions (auth mechanism,
Next.js routing model, test frameworks) also left `[Open]` for the Day 5 technical plan.
**Confirms:** no implementation code, `.spec.md`, `.plan.md`, or `.tasks.md` was generated this
session. Day 1 only.

### 2026-09-04 — emp-internal-transfer.Day2
**Prompted by:** Aditya Hazra (Developer)
**Intent:** Author the initial Feature Specification from the approved Day 1 BRD-001 discovery
output — what is being built, who it serves, testable acceptance criteria, scope boundaries, and
non-functional constraints. No implementation, no `.plan.md`/`.tasks.md`, no finalized API
contract (Day 3).
**Source/context files used:** `.ai-context/BRD.md#BRD-001`, `.ai-context/constitution.md`,
`.ai-context/project_context.md`, `.ai-context/architecture.md` (read and confirmed inapplicable —
still prior-project content), `.agent/rules/int-standards.laravel.md`,
`.agent/rules/int-standards.nextjs.md`.
**Specification created:** `.ai-context/specs/emp-internal-transfer.spec.md` (Draft v1.0) — Intent,
Context, API Contract placeholder, 17 acceptance criteria, an Open Decisions table, Explicitly Out
of Scope, Non-Functional Constraints, Frontend/Backend Responsibility Boundary, and Traceability.
**AC IDs created:** `emp-internal-transfer.AC01`–`AC17` (request initiation AC01–AC02; validation
AC03–AC04; eligibility gate AC05; manager transition AC06–AC07; HR transition AC08–AC09; downstream
processing/completion AC10–AC11; status/pending-action visibility AC12–AC13; authorization
isolation AC14–AC17). No AC written for duplicate/concurrent-transfer handling (Q03) or withdrawal/
cancellation (Q09) — recorded as open instead of asserted.
**Unresolved decisions carried forward (none silently resolved):** Q01 (eligibility criteria),
Q02 (minimum effective-date lead time), Q03 (duplicate/concurrent active-transfer handling), Q06
(org-info stage ownership), Q07 (rule deciding downstream-step applicability), Q08 (rejection/
rollback semantics beyond a basic non-approved transition), Q09 (withdrawal/cancellation), Q11
(RBAC resolution mechanism for "manager"/"HR"). Also noted: auditability is not an approved
constitution requirement for this project, so no audit-logging AC was asserted as fact.
**Confirms:** no implementation code, `.plan.md`, or `.tasks.md` was generated this session. API
contract and spec-derived test cases remain for Day 3. Day 2 only.

### 2026-09-04 — emp-internal-transfer.cleanup
**Prompted by:** Aditya Hazra (Developer)
**Instruction (summary):** Reviewed the Day 2 spec, confirmed intent to proceed to Day 3, and
asked that leftover Empty Floor content be removed from the repo (no longer needed) while its
context is kept in memory.
**Artefacts touched:** Saved persistent memory
(`project_repurposed_from_empty_floor.md` + `MEMORY.md` index) before removing anything. Deleted
`.ai-context/_archive/empty-floor-legacy/`, `.ai-context/SRS.md`, `.ai-context/architecture.md`,
`.ai-context/inventory/`, `.ai-context/templates/`, `.ai-context/source-docs/README.md`,
`.ai-context/source-docs/proposal-extract.md`, `.agent/rules/int-standards.node.md`. Updated
`.agent/rules/.agentignore` (dropped Node/Prisma-specific patterns, added Laravel/Next.js
equivalents). Updated `project_context.md`, `status.md`, and this feature's spec's Context section
to stop referencing the now-removed `architecture.md`.
**Outcome:** Repo now holds no Empty Floor-specific files. Kept: `prompt_history.md` (append-only
by its own rule; old entries clearly demarcated as prior-project), `releases/` and `decisions/`
(always-empty generic scaffolding).
**Follow-up:** None open. Day 3 work begins on the developer's next prompt.

### 2026-09-08 — emp-internal-transfer.Day3
**Prompted by:** Aditya Hazra (Developer)
**Intent:** Complete the Days 1–3 SDD milestone — finalize `emp-internal-transfer.spec.md` with a
full API contract, exception/error contracts, spec-derived unit test cases, broader QA scenarios,
and Laravel↔Next.js contract notes. No plan/tasks/production code. Move spec status to
`In Peer Review (Gate 1)`.
**Source/context files used:** `.ai-context/BRD.md#BRD-001`,
`.ai-context/specs/emp-internal-transfer.spec.md`, `.ai-context/constitution.md`,
`.agent/rules/int-standards.laravel.md`, `.agent/rules/int-standards.nextjs.md`.
`.ai-context/architecture.md` no longer exists (removed after Day 2) — confirmed consistent with
"no existing convention to inherit" rather than treated as a gap in this session's work.
**Pre-work correction (before the Day 3 task itself):** found two more leftover Empty Floor
artefacts missed on 2026-09-04 — root `CLAUDE.md` (rewritten for this project; was still titled
for Empty Floor, pointed at the by-then-deleted `int-standards.node.md`, and stated the old
"blocked pending Phase 1" rule) and `.agent/workflows/` (a Gate 2 reviewer/code-review/
generate-plan/generate-tests toolchain naming Empty Floor's reviewers — deleted outright). Recorded
in persistent memory (`project_repurposed_from_empty_floor.md`).
**API IDs created:** `emp-internal-transfer.API01` (Create Transfer Request — `POST
/api/v1/transfer-requests`), `API02` (Get Transfer Details — `GET /transfer-requests/{id}`),
`API03` (Approval/Stage Action — `POST /transfer-requests/{id}/actions`). Base path, the error
envelope shape, and the 403-vs-404 authorization/not-found split are explicitly flagged in the spec
as proposed decisions for Gate 1, not repository facts — no existing API convention exists to
defer to.
**AC/UT mapping:** `UT01`–`UT17` map 1:1 to `AC01`–`AC17` (see the spec's Unit Test Cases table and
Traceability section for the full BRD→AC→API→UT chain).
**Test cases artifact created:** `.ai-context/test_cases/emp-internal-transfer.test_cases.md` — 11
categories (API, validation/boundary, auth/RBAC, state-transition, concurrency, Laravel contract,
Next.js integration, loading/error/empty UI, accessibility, cross-browser, regression), including
three explicit "gap scenarios" (`VAL-06`, `AUTH-08`, `CONC-03`) tied to Q02/Q11/Q03 that document
what to test once those resolve, not asserted behaviour today.
**Open decisions carried into Gate 1:** Q01 (eligibility criteria), Q02 (effective-date lead time),
Q03 (duplicate/concurrent requests — no API exception written), Q06/Q07 (downstream-step
ownership/applicability), Q08 (rollback depth), Q09 (withdrawal), Q11 (RBAC resolution mechanism).
Plus three specification-level (not business) decisions flagged for Gate 1 confirmation: the
proposed base path, the error envelope shape, and the 403-vs-404 split.
**Confirms:** no `.plan.md`, `.tasks.md`, migration, controller, service, React component, or
production code was created. Spec status set to `In Peer Review (Gate 1)`; Gate 1 is not claimed
as Approved. Gate 1 reviewer recorded as Pending Assignment — the developer was asked to name an
independent reviewer (not the author).

### 2026-09-08 — emp-internal-transfer.Day3.gate1-reviewer
**Prompted by:** Aditya Hazra (Developer)
**Instruction (summary):** Provided Gate 1 Reviewer Name: Sourav Kumar Maity.
**Check performed before recording:** the account's identity signal matched the proposed reviewer
name almost exactly, and the previously-recorded author name ("Shamik Bhattacharya") had no
independent confirmation as a distinct real person — risking an undetected self-review. Asked the
developer to confirm author vs. reviewer were genuinely different people before proceeding, per
the constitution's and Day 3 prompt's "reviewer must not be the author" rule.
**Outcome:** Developer confirmed: author is **Aditya Hazra** (not Shamik Bhattacharya — corrected
everywhere that name appeared: `status.md`, this file's prior Day 1/2/3 entries, and
`specs/emp-internal-transfer.spec.md`'s Gate 1 Review block); Gate 1 reviewer is **Sourav Kumar
Maity**, confirmed distinct from the author. Recorded reviewer in the spec's Gate 1 Review block
and in `status.md`'s Programme status table.
**Artefacts touched:** `specs/emp-internal-transfer.spec.md`, `status.md`, `prompt_history.md`
(this file — author-name correction applied to all prior 2026-09-08 and earlier entries in this
session).
**Follow-up:** Gate 1 is **not** Approved by this entry — only the reviewer assignment and the
author-name correction are recorded. The reviewer's actual Gate 1 decision (Approved / Changes
Requested) is a separate, later step, to be recorded when Sourav Kumar Maity provides it.
