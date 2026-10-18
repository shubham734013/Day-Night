# Architecture — Day Night GymOS

**Status:** Proposed baseline; verify against the actual repository before implementation.  
**Scope:** Single-gym system for Day Night Gym. No multi-tenant SaaS architecture.

## 1. Architecture principles

1. Keep the system modular, not distributed without a demonstrated need.
2. Enforce authorization and business invariants in the API/domain layer and database.
3. Use one authoritative source of truth for financial records.
4. Make sensitive actions auditable and retry-prone operations idempotent.
5. Keep frontend presentation separate from domain rules.
6. Avoid platform abstractions for hypothetical future gyms.
7. Prefer reversible, migration-backed changes and tested backups.

## 2. Proposed repository layout

The latest blueprint referenced a pnpm workspace. Retain it only if it matches the current repository; do not restructure the repository merely to match this document.

```text
.
├── apps/
│   ├── api/                 # Fastify API, authentication, domain services
│   └── web/                 # React portals and shared UI
├── packages/
│   └── shared/              # Shared schemas, types, constants (no server secrets)
├── migrations/              # If not colocated with apps/api
├── docs/
├── pnpm-workspace.yaml
└── package.json
```

If the existing repository uses another structure, document the real layout and adapt the boundaries instead of performing a broad rewrite.

## 3. Technology baseline

The latest project blueprint specified:
- Package manager/workspaces: pnpm workspaces.
- API: TypeScript, Fastify, Zod type provider, RFC 7807-style problem responses, Pino structured logging.
- Database: PostgreSQL 17+, Drizzle ORM, UUIDv7 identifiers.
- Web: React, Vite, React Router, TanStack Query, Tailwind CSS, shadcn/ui, motion where justified.
- Front-desk offline capability: Dexie/IndexedDB for a narrowly scoped attendance queue only.
- PDF generation: pdfmake with Noto fonts for reliable Devanagari rendering.
- Background work: pg-boss only where asynchronous processing is justified.
- Payments: provider-hosted checkout/verified server callbacks if an online gateway is approved.

These are design targets, not proof that every dependency is installed or every feature exists. Verify versions and package names against manifests before coding.

## 4. Logical modules

### Identity and access
- Authentication, sessions, password reset, staff invitations, role assignment, and revocation.
- Roles: owner/admin, front desk, trainer, member.
- Use explicit permissions and object-level checks. A role is not a substitute for checking ownership/assignment.
- No `/platform` or cross-gym administration module.

### Gym operations
- Member profiles and stable member IDs.
- Membership plans and membership lifecycle.
- Attendance events and staff overrides.
- Staff/trainer assignments.
- Gym settings and operating hours.

### Billing and finance
- Invoices, invoice lines, payments, allocations, refunds/adjustments, expenses, reconciliation, and reports.
- Monetary storage in integer paise / integer minor units; do not use floating-point arithmetic for money.
- Keep invoice totals derived and validated server-side.
- Record corrections as linked adjustments/reversals rather than overwriting settled history.

### Fitness
- Exercise catalogue, workout plans, workout-plan assignments, session logs, progress measurements.
- Access restricted to the member and authorized trainer/management.
- Nutrition plans, if approved, should use explicit source/review metadata and avoid medical claims.

### Notifications and jobs
- Queue renewal reminders, receipt delivery, exports, and other non-blocking work only when configured.
- Use retry limits, deduplication keys, delivery status, and dead-letter/manual recovery procedures.
- Do not enqueue sensitive notifications before consent and provider setup are defined.

### Audit and observability
- Append-only audit records for permission changes, member status changes, financial corrections, refunds, overrides, and administrative actions.
- Structured logs with correlation/request IDs and redacted personal data.
- Health/readiness checks and error monitoring.

## 5. Data model — conceptual entities

| Entity | Purpose / key relationships |
|---|---|
| `User` | Login identity, status, credential/session metadata |
| `Role` / `Permission` | Explicit authorization policy; may be enum-backed if scope remains simple |
| `MemberProfile` | Member-specific profile linked to a user where accounts exist |
| `StaffProfile` | Staff/trainer profile and active status |
| `TrainerAssignment` | Explicit trainer-to-member relationship |
| `MembershipPlan` | Configurable product, price, duration, terms |
| `Membership` | Member, plan snapshot, start/end, state and lifecycle history |
| `Invoice` / `InvoiceLine` | Amount owed and line-item snapshot |
| `Payment` | Recorded payment, method, provider reference, status, idempotency key |
| `PaymentAllocation` | Allocation of payments to invoices, including partial payments |
| `Refund` / `Adjustment` | Traceable reversal/correction linked to original transaction |
| `AttendanceEvent` | Member, timestamp, source, idempotency key, sync/override metadata |
| `WorkoutPlan` / `Exercise` | Reusable workout definition |
| `WorkoutAssignment` | Member, trainer, plan, start/end and state |
| `WorkoutLog` / `ProgressEntry` | Member progress history |
| `Expense` | Gym expense record and supporting references |
| `ReconciliationRun` / `ReconciliationItem` | Match recorded activity to cash/gateway statements |
| `AuditLog` | Actor, action, target, timestamp, request ID, minimal safe metadata |
| `Notification` | Channel, recipient reference, template/version, delivery status |
| `IdempotencyRecord` | Scope/key, request fingerprint, result reference, expiry as appropriate |

Final schema must be based on the actual business rules and migrations. Avoid creating redundant tables before validating workflow needs.

## 6. Database and transaction rules

- Use PostgreSQL constraints, unique indexes, foreign keys, and transactions for integrity.
- UUIDv7 is the current proposal for sortable identifiers; verify library and database support.
- Do not add `gym_id`-based multi-tenant RLS from the SaaS blueprint. The confirmed product scope is one gym, not tenant-per-gym SaaS.
- If RLS is retained for defense-in-depth, design it around actual security boundaries and test connection pooling, transaction scope, and role behavior. Do not copy a tenant RLS pattern mechanically.
- Use migrations for every schema change; never rely on production auto-sync.
- Use a transaction for invoice/payment allocation and other multi-record financial changes.
- Use database uniqueness plus idempotency logic to prevent duplicate payment callbacks and duplicate check-ins.
- Use UTC timestamps and a configured display timezone.
- Avoid hard deletes for financial/audit/attendance records. Prefer state transitions and retention-approved anonymization.

## 7. API conventions

- Version external API paths (for example `/api/v1`) if this is consistent with the current repository.
- Validate request params, query, body, and relevant response shapes with Zod.
- Use RFC 7807-compatible problem responses with stable error codes and safe user-facing details.
- Authenticate before protected operations; authorize each object/action after loading the relevant record.
- Derive actor identity and permissions from the authenticated session, never from client-supplied role fields.
- Use pagination and bounded filters for list endpoints.
- Require `Idempotency-Key` for payment initiation and other operations where retries can duplicate effects.
- Do not return internal stack traces, secrets, raw database errors, or unnecessary personal data.
- Document API contracts and backward-incompatible changes.

## 8. Frontend structure

Suggested domains:
- `auth`
- `members`
- `memberships`
- `attendance`
- `billing`
- `fitness`
- `reports`
- `settings`
- `audit`

Use React Router for portal routes and guards for user experience, but enforce the same policy in the API. TanStack Query should own server-state caching and invalidation; avoid duplicating server state in unrelated global stores.

Portal boundaries:
- `/admin`: full authorized management capabilities.
- `/desk`: high-speed member and attendance workflows with only granted financial actions.
- `/trainer`: assigned-member fitness workflows.
- `/m`: member-owned data only.

## 9. Offline attendance design

Offline support is a reliability feature with integrity risks:
1. Cache the minimum required member lookup data, encrypted where feasible and appropriate to platform capabilities.
2. Queue attendance events locally with unique event IDs and creation timestamps.
3. Mark each item `pending`, `synced`, or `rejected`; do not represent pending as accepted.
4. On reconnection, submit events idempotently.
5. The server revalidates the member, event time policy, and authorization.
6. Surface conflicts and rejection reasons to authorized staff.
7. Define device logout, local cache clearing, retention, and shared-device safeguards.
8. Do not support offline payments in the initial design.

## 10. Payment integration

- Customer manually initiates each payment; no recurring auto-debit assumption.
- Use hosted/provider-approved checkout where possible.
- Verify provider callbacks/webhooks server-side, including signature and amount/currency/reference checks.
- Treat browser redirect success as informational only; authoritative status comes from the verified server callback/query.
- Use idempotency keys and unique provider event IDs.
- Record gateway fees, refunds, failures, and reconciliation data only where supported by the chosen provider.
- Never store raw card credentials.
- Confirm the provider's current commercial terms before estimating transaction costs or promising payment methods.

## 11. Security and privacy

- Least privilege, session revocation, rate limits, secure cookie configuration, CSRF defense as applicable, and secure headers.
- Validate uploads and exports; protect against spreadsheet formula injection in CSV exports.
- Redact PII in logs and audit metadata.
- Use secrets manager/environment variables and separate development/test/production credentials.
- Restrict access to member health/fitness notes to explicit business need.
- Record privileged reads only if policy and privacy requirements call for them; always audit high-impact mutations.
- No production data copied to development without approved minimization/anonymization.

## 12. Deployment and operations

- Environments: local, test/CI, staging, production only if these are actually provisioned.
- CI: typecheck, lint, unit/integration tests, build, migration validation, security checks as configured.
- Deployment requires a known commit, green required checks, migration plan, backup verification, rollback plan, and owner approval.
- Run scheduled backups and periodically test restore into an isolated environment.
- Document secrets rotation, incident contact, job retry/replay, and recovery procedures.
- Do not claim production readiness without deployment-specific evidence.

## 13. Architecture decision log

Record material decisions in `Memory.md`, including:
- Why the product is single-gym only.
- Confirmed framework/database versions.
- Payment and notification provider choices.
- Authorization model and role matrix.
- Offline attendance consistency model.
- Retention, backups, and hosting decisions.
