# Project Memory and Decision Register — Day Night GymOS

**Purpose:** Persistent, concise source of truth for project context, constraints, and approved decisions. Update this file whenever a decision changes.

## 1. Product identity

- **Name:** Day Night GymOS (working product name).
- **Real-world scope:** Day Night Gym only.
- **Primary purpose:** Manage gym operations and member fitness in one application.
- **Portals:** `/admin`, `/desk`, `/trainer`, `/m`.
- **Deployment intent:** Use a deployment model that fits Day Night Gym's actual operating needs. Do not infer that a multi-tenant SaaS service is approved.

## 2. Confirmed constraints

1. Single-gym scope only.
2. Do not add multi-gym SaaS, tenant provisioning, platform super-admin, marketplace, or multi-gym billing.
3. Operations and fitness are both important; neither should be treated as the only product.
4. Member payments are for Day Night Gym. Any online gateway must support the intended direct-to-gym flow and be verified before implementation.
5. Payments are manually initiated by customers; do not assume recurring auto-debit.
6. Financial records, attendance history, and audit records must not be hard-deleted.
7. Security, privacy, auditability, and reliable recovery are product requirements, not optional polish.
8. AI functionality is deferred until the core workflows are stable and explicitly approved.
9. Do not treat a passing build or a green test suite as proof of production readiness.
10. Never claim an implementation or test has been completed without evidence from the repository/CI or an actual execution result.

## 3. Latest proposed technology baseline

These were present in the shared project blueprint and must be checked against the actual repository before being treated as implemented:
- pnpm workspaces.
- TypeScript API using Fastify, Zod validation/type provider, RFC 7807-style errors, Pino logging.
- PostgreSQL 17+, Drizzle ORM, UUIDv7.
- React + Vite, React Router, TanStack Query, Tailwind CSS, shadcn/ui, motion where appropriate.
- Dexie/IndexedDB for a limited offline attendance queue.
- pdfmake with Noto fonts for Devanagari-capable PDFs.
- pg-boss for justified background jobs.

If the repository differs, document the real stack and propose changes separately; do not perform a broad rewrite solely to match this list.

## 4. Scope baseline

### Core
- Authentication and staff access.
- Member profiles and stable unique IDs.
- Membership plans, lifecycle, renewals, expiry.
- Attendance and authorized overrides.
- Invoices, receipts, payment recording, outstanding balances.
- Trainers, member assignments, workouts, progress.
- Member self-service portal.
- Admin reports and audit log.
- Expenses/reconciliation when core financial flows are stable.
- Backup/restore and deployment documentation.

### Later / approval required
- Offline attendance and QR anti-replay.
- Automated WhatsApp bills and reminders.
- AI assistants, AI coaching, or generated nutrition plans.
- Payroll/PT commissions, advanced double-entry accounting, GST workflows.
- Multi-gym or SaaS functionality (currently out of scope).

## 5. Architecture decisions

| ID | Decision | Status | Rationale / notes |
|---|---|---|---|
| DEC-001 | Product is for Day Night Gym only | Confirmed | Avoid unnecessary multi-tenant complexity |
| DEC-002 | Four portals: admin, desk, trainer, member | Confirmed | Maps to actual user groups |
| DEC-003 | Operations and fitness have equal product importance | Confirmed | Both are central workflows |
| DEC-004 | No recurring auto-debit assumption | Confirmed | Customer manually initiates payment |
| DEC-005 | Use integer minor units for money | Proposed baseline | Avoid floating-point rounding errors |
| DEC-006 | No hard delete for financial/audit/attendance history | Confirmed | Preserve accountability and reconciliation |
| DEC-007 | AI features deferred | Confirmed for initial scope | Stabilize core workflows first |
| DEC-008 | pnpm/Fastify/PostgreSQL/Drizzle stack | Verify against repo | Blueprint proposal, not proof of implementation |
| DEC-009 | Offline queue limited to attendance | Proposed | Avoid offline financial inconsistency |
| DEC-010 | Payment gateway/provider | Open | Select only after verifying fees, settlement, and supported methods |
| DEC-011 | Notification provider and consent | Open | Must define consent, retries, opt-out and cost |
| DEC-012 | Hosting and backup targets | Open | Record actual provider and restore objectives |
| DEC-013 | Membership/refund/override policy | Open | Owner approval required |

## 6. Source-of-truth rules

- Approved product decisions belong here and in the relevant requirements document.
- Current implementation facts must be verified against repository code and manifests.
- Test results must cite exact command/CI run and commit.
- Keep unresolved questions in `Tracker.md`; do not silently convert assumptions into requirements.
- When documents conflict, the confirmed single-gym scope in this file takes precedence over old multi-tenant blueprint text.
- Record a decision change with date, decision, rationale, and affected documents.

## 7. Change log

- **v1.0:** Initial documentation baseline created from the available Day Night GymOS project context. Implementation-specific claims intentionally remain marked for verification.
