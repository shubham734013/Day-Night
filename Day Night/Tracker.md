# Project Tracker — Day Night GymOS

**Status:** Initial planning baseline. Verify current repository state before marking implementation tasks complete.  
**Rule:** Do not mark a task `Done` without code/CI evidence or an explicitly recorded owner decision.

## 1. Status definitions

- `Not started` — no implementation evidence.
- `In progress` — active work exists but acceptance is incomplete.
- `Blocked` — requires a decision, provider, access, or dependency.
- `Review` — implementation exists and needs verification.
- `Done` — acceptance criteria verified and evidence linked.
- `Deferred` — intentionally outside the current release.

## 2. Release phases

### Phase 0 — Repository and product baseline
| ID | Task | Priority | Status | Acceptance / evidence |
|---|---|---:|---|---|
| P0-001 | Audit repository structure, manifests, scripts and current implementation | P0 | Not started | Document actual stack and scripts; record commit SHA |
| P0-002 | Reconcile code with single-gym scope and four portals | P0 | Not started | No unapproved multi-tenant/platform surface |
| P0-003 | Establish CI checks: typecheck, lint, tests, build | P0 | Not started | Required checks and exact run links recorded |
| P0-004 | Document environment variables and safe local setup | P0 | Not started | `.env.example` only; no real secrets |
| P0-005 | Confirm owner decisions for membership, refund, timezone and staff override policies | P0 | Blocked | Decisions recorded in `Memory.md` |

### Phase 1 — Identity and access
| ID | Task | Priority | Status | Acceptance / evidence |
|---|---|---:|---|---|
| IAM-001 | Authentication and secure session lifecycle | P0 | Not started | Login/logout/expiry/revocation tests |
| IAM-002 | Roles and explicit permission matrix | P0 | Not started | Documented admin/desk/trainer/member permissions |
| IAM-003 | Server-side object-level authorization | P0 | Not started | Positive and negative access tests for every domain |
| IAM-004 | Staff account activation/deactivation and access revocation | P0 | Not started | Deactivated staff cannot continue protected actions |
| IAM-005 | Rate limiting, safe errors, redacted structured logs | P0 | Not started | Abuse/error tests and config verified |

### Phase 2 — Members and memberships
| ID | Task | Priority | Status | Acceptance / evidence |
|---|---|---:|---|---|
| MEM-001 | Member profile and stable unique member ID | P0 | Not started | Duplicate and validation tests |
| MEM-002 | Member search, pagination and profile view | P0 | Not started | Bounded queries; permission-safe search |
| MEM-003 | Membership plans and price/duration configuration | P0 | Not started | Validation and permission tests |
| MEM-004 | Membership lifecycle, renewals, pauses and expiry | P0 | Blocked | Owner policy decisions resolved; history preserved |
| MEM-005 | Member self-service membership view | P0 | Not started | Member sees own data only |

### Phase 3 — Billing and finance
| ID | Task | Priority | Status | Acceptance / evidence |
|---|---|---:|---|---|
| FIN-001 | Invoice and receipt generation | P0 | Not started | Amount calculations and PDF/print checks |
| FIN-002 | Manual cash/UPI payment recording | P0 | Not started | Permissions, balance and audit tests |
| FIN-003 | Partial payments and allocations | P0 | Not started | Transactional consistency and balance tests |
| FIN-004 | Idempotency for financial operations | P0 | Not started | Concurrent/retry tests prevent duplicates |
| FIN-005 | Refunds/reversals/adjustments | P0 | Blocked | Owner policy and permission/audit rules approved |
| FIN-006 | Online gateway integration | P1 | Blocked | Provider, commercial terms, callback verification and test mode approved |
| FIN-007 | Cash/gateway reconciliation | P1 | Not started | Variance report and resolution history |
| FIN-008 | Expenses and date-filtered financial reports | P1 | Not started | Totals match authoritative transaction fixtures |

### Phase 4 — Attendance and front desk
| ID | Task | Priority | Status | Acceptance / evidence |
|---|---|---:|---|---|
| ATT-001 | Fast member lookup and online check-in | P0 | Not started | Front-desk smoke test and API tests |
| ATT-002 | Duplicate check-in prevention | P0 | Not started | Idempotency/concurrency tests |
| ATT-003 | Membership validity policy and staff override | P0 | Blocked | Owner policy approved; override audited |
| ATT-004 | Attendance history and reporting | P0 | Not started | Filters and permission tests |
| ATT-005 | QR check-in with replay protection | P1 | Deferred | Threat model and token lifecycle defined |
| ATT-006 | Offline attendance queue and sync conflict UI | P1 | Deferred | Pending/synced/rejected tests and shared-device review |

### Phase 5 — Fitness and trainer workflows
| ID | Task | Priority | Status | Acceptance / evidence |
|---|---|---:|---|---|
| FIT-001 | Trainer profiles and member assignments | P0 | Not started | Trainers see only assigned/authorized members |
| FIT-002 | Exercise catalogue and workout plan editor | P0 | Not started | Validation, ordering and usability tests |
| FIT-003 | Assign workouts to members | P0 | Not started | Authorization and lifecycle tests |
| FIT-004 | Member workout view and session logging | P0 | Not started | Member-owned data and history tests |
| FIT-005 | Progress measurements and charts | P1 | Not started | Validated values and accessible text alternatives |
| FIT-006 | Reviewed, sourced nutrition plans | P2 | Deferred | Explicit product approval and review model required |
| FIT-007 | AI fitness/nutrition assistant | P2 | Deferred | Safety, privacy, cost, and approval required |

### Phase 6 — Admin, audit and reporting
| ID | Task | Priority | Status | Acceptance / evidence |
|---|---|---:|---|---|
| ADM-001 | Admin overview with defined KPIs | P0 | Not started | KPI formulas documented; no fabricated data |
| ADM-002 | Audit log for privileged and financial mutations | P0 | Not started | Append-only behavior and access tests |
| ADM-003 | Staff management and permission changes | P0 | Not started | Changes are authorized and audited |
| ADM-004 | Report filters, pagination and exports | P1 | Not started | Export matches filters and respects permissions |
| ADM-005 | Renewal reminders | P1 | Blocked | Provider, consent, templates and opt-out approved |
| ADM-006 | WhatsApp bills and campaigns | P2 | Deferred | Explicit approval and compliance/provider setup |

### Phase 7 — Reliability, deployment and release
| ID | Task | Priority | Status | Acceptance / evidence |
|---|---|---:|---|---|
| OPS-001 | Database migrations and migration validation | P0 | Not started | Fresh install and upgrade path tested |
| OPS-002 | Backup policy and restore drill | P0 | Not started | Successful restore in isolated environment |
| OPS-003 | Health/readiness checks and structured logs | P0 | Not started | Operational runbook and failure checks |
| OPS-004 | CI security/dependency checks | P1 | Not started | Results reviewed; exceptions documented |
| OPS-005 | Deployment, rollback and incident runbook | P0 | Not started | Owner-approved steps and recovery path |
| OPS-006 | End-to-end role-based smoke tests | P0 | Not started | Four portals and critical workflows covered |
| OPS-007 | Production-readiness review | P0 | Not started | No unresolved critical/high issues; evidence tied to commit |

## 3. Release gates

A release must not proceed until:
- [ ] Single-gym scope and four portal boundaries are respected.
- [ ] All P0 acceptance criteria are verified.
- [ ] Authorization tests cover both allowed and denied access.
- [ ] Payment, invoice and balance calculations reconcile.
- [ ] Idempotency/concurrency checks pass for retry-prone operations.
- [ ] Migration and restore procedures are tested.
- [ ] Critical/high security findings are resolved or formally accepted by the owner.
- [ ] Required CI checks pass on the exact release commit.
- [ ] Rollback, support ownership, and incident procedures are documented.
- [ ] Owner explicitly approves the release.

## 4. Current blockers / decisions to resolve

1. Confirm the actual repository stack and existing implementation before coding to the proposed architecture.
2. Choose and verify the payment gateway, fee structure, settlement behavior, and supported payment methods.
3. Define membership pause/freeze, refund, discount, and override rules.
4. Confirm gym timezone and business-day boundaries.
5. Choose notification providers and define consent/opt-out handling.
6. Confirm hosting, backup frequency, recovery objectives, and deployment ownership.
7. Decide whether offline attendance is required for the first release or can ship as P1.

## 5. Evidence log

For each completed task, record:
- Task ID.
- Commit SHA / pull request.
- Tests and exact command or CI run URL.
- Manual verification performed.
- Known limitations.
- Reviewer/owner approval where required.

No task is considered complete based only on an AI-generated summary.
