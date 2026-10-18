# Engineering, Security, and AI-Agent Rules — Day Night GymOS

These rules apply to contributors, coding agents, code reviews, and automated changes.

## 1. Scope guardrails

- Build for Day Night Gym only.
- Do not implement multi-tenancy, tenant provisioning, a `/platform` portal, platform super-admin, multi-gym billing, or a gym marketplace.
- Preserve four portals: `/admin`, `/desk`, `/trainer`, `/m`.
- Treat operations and fitness as first-class domains.
- Do not implement deferred AI, WhatsApp automation, payroll/PT commissions, advanced accounting, or GST filing without explicit approval.
- Do not make broad framework migrations or repository restructures without a written reason and an incremental plan.

## 2. Repository workflow

1. Inspect the repository structure, manifests, existing conventions, and relevant tests before editing.
2. Establish the current branch and working-tree state; do not overwrite unrelated user changes.
3. Make focused, reviewable changes with a clear purpose.
4. Add or update tests for every behavior change and bug fix.
5. Run the narrowest relevant tests first, then typecheck/lint/build and broader tests available in the repository.
6. Report exact commands, results, commit SHA, and CI run links where available.
7. Do not state that tests passed if they were not run.
8. Do not hide skipped, flaky, or failing tests in a summary.
9. Avoid unrelated formatting churn and generated-file changes unless required.

## 3. Security rules

- All authorization must be enforced server-side.
- Validate role, action, and record ownership/assignment on every protected operation.
- Never trust client-supplied user IDs, role claims, prices, totals, payment status, or permissions.
- Validate request bodies, path parameters, and query parameters at API boundaries.
- Apply least privilege and revoke access when accounts/staff are deactivated.
- Protect sessions and cookies; use CSRF controls when applicable.
- Rate-limit login, password reset, check-in abuse, and other sensitive endpoints.
- Never commit credentials, private keys, production data, or real environment files.
- Never log passwords, tokens, full payment credentials, or unnecessary sensitive member information.
- Keep error responses safe; do not expose stack traces or internal database details to clients.
- Add tests for both allowed and denied access, including object-level cross-user attempts.
- Protect CSV/PDF exports and uploads; avoid leaking records through search, counts, autocomplete, or export endpoints.
- Do not copy production personal data into development/test environments without explicit approval and minimization/anonymization.

## 4. Financial integrity rules

- Represent currency as integer paise/minor units; do not use floating-point arithmetic for money.
- Compute totals and balances on the server from authoritative records.
- Use database transactions for multi-record financial mutations.
- Use idempotency keys and unique provider event IDs for retry-prone payment operations.
- Verify gateway signatures and transaction details server-side.
- Never mark a payment successful based only on a browser redirect.
- Do not overwrite settled payment history; use linked refunds, reversals, and adjustments.
- Distinguish invoice value, amount collected, refund amount, and outstanding balance in UI and reports.
- Require appropriate permissions and an audit reason for refunds, manual adjustments, and overrides.
- Never test a live payment flow without explicit approval and a provider-approved test setup.

## 5. Data and migration rules

- Every schema change must have a migration and an appropriate rollback/recovery plan.
- Use database constraints for uniqueness and relationships wherever possible.
- Do not hard-delete financial records, attendance history, or audit events.
- Use safe archival/deactivation and approved retention/anonymization policies.
- Keep timestamps consistent and use the configured gym timezone for display/business rules.
- Bound pagination, exports, request size, and expensive filters.
- Do not run destructive migrations, production backfills, or live data mutation without explicit authorization.
- Verify backup and restore before relying on a migration rollback strategy.

## 6. Frontend rules

- UI guards improve navigation but never replace API authorization.
- Use TanStack Query (if present) for server-state fetching, cache invalidation, and mutation lifecycle.
- Avoid duplicate sources of truth for server data.
- Provide loading, empty, error, validation, and permission-denied states.
- Do not display success until the server confirms the action.
- Do not optimistically confirm financial actions or unsynced attendance.
- Preserve form values after recoverable errors.
- Respect keyboard navigation, focus visibility, semantic labels, contrast, and reduced-motion preferences.
- Do not place secrets or unnecessary personal data in local storage or IndexedDB.

## 7. Offline attendance rules

- Offline support is limited to attendance unless scope is explicitly changed.
- Pending events must be visibly marked as pending.
- Every queued event needs a stable unique event ID and idempotent synchronization.
- Server revalidates authorization and business rules at synchronization time.
- Conflicts and rejected events must be visible and recoverable.
- Shared-device logout must clear or safely isolate cached personal data.
- Never represent a local queued event as server-confirmed attendance.
- Offline payments are prohibited in the initial release.

## 8. API and observability rules

- Keep API contracts typed and validated.
- Use stable, documented error codes and safe problem responses.
- Include request/correlation IDs in logs where possible.
- Redact sensitive fields before logging.
- Add useful health/readiness checks and monitor background job failures.
- Retry jobs only when operations are idempotent; bound retries and provide recovery.
- Keep logs actionable and avoid collecting data without a clear operational need.

## 9. AI coding-agent operating protocol

Before coding:
1. Restate the requested outcome and acceptance criteria.
2. Read the relevant docs and inspect current code.
3. List files likely to change and risks.
4. Identify assumptions and ask only if an unresolved decision blocks safe progress.

While coding:
1. Make the smallest coherent change.
2. Do not invent APIs, schema fields, scripts, or successful outputs.
3. Keep changes inside the approved scope.
4. Add regression tests that fail before the fix and pass after it.
5. Do not weaken/delete tests merely to get green CI.
6. Do not assert that a vulnerability is fixed if the test only demonstrates that it exists.

After coding:
1. Report files changed and behavioral impact.
2. Report exact test commands and their actual results.
3. Report commit SHA and relevant CI status when available.
4. Separate verified facts from assumptions and untested risks.
5. List remaining blockers and follow-up tasks.
6. Do not merge, deploy, run live migrations/backfills, alter production settings, or initiate real payments without explicit authorization.

## 10. Definition of done

A task is done only when:
- Acceptance criteria are met.
- Regression tests cover success and failure/authorization cases.
- Relevant typecheck/lint/build/tests have been run and reported truthfully.
- No unrelated changes or secrets were introduced.
- Documentation and `Tracker.md` are updated when behavior or decisions change.
- Security, data integrity, and rollback implications are addressed.
