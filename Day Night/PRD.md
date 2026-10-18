# Product Requirements Document — Day Night GymOS

**Document status:** Baseline v1.0  
**Product:** Day Night GymOS  
**Deployment scope:** One gym — Day Night Gym  
**Primary audience:** Gym owner/management, front-desk staff, trainers, and members

## 1. Product summary

Day Night GymOS is a secure, mobile-friendly gym management and fitness platform for operating **Day Night Gym only**. It brings gym administration, front-desk workflows, membership and billing, attendance, trainer-led fitness plans, member progress, and owner reporting into one coherent application.

This is not a multi-gym SaaS product. Do not build tenant provisioning, a platform super-admin portal, cross-gym billing, a gym marketplace, or workflows for onboarding unrelated gyms unless the product owner explicitly changes scope.

## 2. Product goals

1. Make member registration, membership management, renewals, receipts, and attendance fast and reliable.
2. Give management a trustworthy view of collections, outstanding balances, expenses, membership status, and operational activity.
3. Give trainers a practical way to manage assigned members, workouts, and progress.
4. Give members a clear view of their membership, attendance, assigned workouts, and progress.
5. Preserve financial and security accountability through immutable audit history, reconciliation, and controlled permissions.
6. Keep essential front-desk attendance usable during temporary connectivity loss where technically safe.
7. Support deployment and backup/restore practices appropriate to the agreed Day Night Gym environment.

## 3. Non-goals and exclusions

- Multi-tenant SaaS, tenant provisioning, and a `/platform` portal.
- Platform-level super-admin or multi-gym owner dashboard.
- Gym marketplace or cross-gym membership.
- Automated AI coaching or AI-generated meal plans in the initial release.
- WhatsApp automation until provider, consent, templates, costs, retries, and opt-out handling are approved.
- Advanced accounting, payroll automation, PT commission automation, or GST filing unless explicitly promoted into a later approved scope.
- Offline financial transactions. Offline mode must not fabricate successful payments or silently create duplicate attendance records.
- Hard deletion of financial records, attendance history, or audit events.

## 4. User roles and portals

| Portal | Intended users | Primary responsibilities |
|---|---|---|
| `/admin` | Owner and authorized managers | Gym settings, staff access, members, plans, billing, reports, expenses, reconciliation, audit |
| `/desk` | Front-desk staff | Member lookup, registration, membership sales/renewals, receipts, check-in, payment recording subject to permission |
| `/trainer` | Trainers | Assigned members, workout plans, session notes, progress tracking; access limited to assigned or explicitly authorized members |
| `/m` | Members | Own profile, membership status, receipts, attendance history, assigned workouts, progress entries and account settings |

The role-to-permission matrix must be explicit and enforced on the server. Hiding a button is not authorization.

## 5. Core workflows

### 5.1 Member lifecycle
- Create a member with a stable, unique member identifier.
- Capture only necessary personal information and record consent where applicable.
- Assign a membership plan and validity period.
- Support renewal, pause/freeze, expiry, cancellation, and reactivation according to gym policy.
- Preserve historical memberships and payment records.
- Prevent accidental duplicate members using carefully designed matching and staff confirmation rather than destructive auto-merging.

### 5.2 Memberships and billing
- Maintain configurable plans, durations, prices, and applicable discounts.
- Create invoices/receipts for membership sales and renewals.
- Support manual payment recording for approved methods such as cash and UPI, including partial payments where enabled.
- Show amount due, amount paid, and outstanding balance consistently.
- Record refunds and adjustments as traceable transactions, not edits that erase history.
- If an online gateway is enabled, use a supported provider checkout flow. The customer must initiate payment manually; do not assume recurring auto-debit.
- Payments are intended for Day Night Gym. Never route or settle funds to an unrelated gym.
- Store monetary values as integer paise in the database and API contracts where practical; format rupees only at display boundaries.
- Use idempotency keys for payment creation/callback processing and other retry-prone financial operations.

### 5.3 Attendance
- Support front-desk member search and fast check-in.
- Support QR check-in only after identity, replay, expiry, and duplicate-scan controls are specified.
- Validate membership status and gym policy, while allowing an authorized staff override with a reason and audit event.
- Handle duplicate requests idempotently.
- Offline check-in may queue signed/identifiable events locally and synchronize later with conflict reporting. The UI must label pending events as pending, never as server-confirmed.
- Do not store unnecessary sensitive data in browser storage.

### 5.4 Fitness and trainer workflows
- Trainers can view only members assigned to them or members explicitly authorized by management.
- Create and assign workout plans, exercises, sets, reps, rest periods, and notes.
- Members can view their assigned plans and log permitted progress.
- Progress history should be append-oriented so that changes remain understandable.
- Nutrition/meal plans, if included, must be reviewed, sourced where appropriate, portion-based, and clearly distinguished from medical advice.
- Advanced AI coaching is out of the initial release.

### 5.5 Finance and management reporting
- Dashboard KPIs should distinguish cash collected from invoices raised and balances outstanding.
- Report revenue by date range, payment method, membership plan, and staff member where permitted.
- Track expenses with category, date, amount, payment method, note, and supporting reference if available.
- Reconcile cash/UPI/gateway collections against recorded transactions.
- Exports must respect the same permissions and filters as the screen.
- Financial totals must be calculated from authoritative records, not client-supplied totals.

### 5.6 Notifications
- In-app and email/SMS/WhatsApp channels are separate capabilities and must be configured intentionally.
- Bills or renewal reminders must not be sent until the channel, templates, recipient consent, provider configuration, retries, and opt-out behavior are defined.
- Notification failures must be observable and retry-safe; they must not duplicate a payment or membership mutation.

## 6. Functional requirements

### P0 — Required for first usable release
- Authentication, session lifecycle, role-based authorization, and staff access management.
- Member CRUD with safe archival/deactivation and unique member IDs.
- Membership plans, member memberships, renewals, expiry handling, and status history.
- Invoices, receipts, payment recording, outstanding balances, and refund/adjustment history.
- Attendance check-in, history, duplicate prevention, and authorized overrides.
- Trainer-to-member assignment and basic workout plan management.
- Member portal for membership, receipts, attendance, and assigned workouts.
- Management dashboard and date-filtered operational/financial reports.
- Audit log for sensitive actions and finance/permission changes.
- Database migration strategy, backup/restore runbook, error handling, and automated tests.

### P1 — After P0 is stable
- Offline attendance queue with explicit pending/synced/rejected states.
- QR attendance with anti-replay protections.
- Cash and gateway reconciliation workflows.
- Expenses and richer reports/exports.
- Renewal reminders using approved notification providers.
- Member progress charts and improved trainer notes.
- Self-hosting/deployment documentation if the deployment owner requires it.

### P2 — Requires explicit approval
- AI assistants or AI fitness/nutrition recommendations.
- Automated payroll or PT commissions.
- Full double-entry accounting and GST-specific workflows.
- WhatsApp bills and campaign automation.
- Marketplace, multi-gym, or SaaS functionality.
- Advanced offline synchronization beyond attendance.

## 7. Non-functional requirements

### Security and privacy
- Enforce authorization on the server for every read and mutation.
- Use secure, HTTP-only cookies or an explicitly reviewed equivalent session design.
- Apply CSRF protection where cookie-based authentication requires it.
- Hash passwords using a modern password-hashing algorithm and appropriate parameters.
- Validate input at the API boundary and reject unknown/invalid fields where practical.
- Rate-limit authentication and abuse-prone endpoints.
- Protect secrets in environment configuration; never commit real credentials.
- Minimize personal data in logs; never log passwords, session tokens, full payment credentials, or sensitive member notes.
- Use append-only audit events for privileged actions and financial corrections.
- Use database constraints and transactions for financial invariants.

### Reliability and performance
- Paginate potentially large lists and bound filters, exports, and request payloads.
- Use transactions for operations that must update multiple related records atomically.
- Make retry-prone endpoints idempotent.
- Use background jobs for non-critical work; jobs must be retry-safe and observable.
- Define health/readiness checks, structured logs, and actionable errors.
- Set and test reasonable performance budgets after representative data volumes are known.

### Accessibility and usability
- Responsive layouts for desktop front desk and mobile member use.
- Keyboard-accessible forms, visible focus, semantic labels, and readable contrast.
- Clear loading, empty, validation, success, and failure states.
- Confirm destructive or financially consequential actions and explain their effects.

### Data integrity
- Use stable primary keys and database-level uniqueness/foreign-key constraints.
- Do not hard-delete invoices, payments, attendance events, or audit records.
- Store timestamps consistently in UTC and display them in the gym's configured timezone.
- Define retention and export policies before collecting more personal data than needed.

## 8. Success measures

Baseline values must be measured rather than invented. Track:
- Median time to check in a known member.
- Duplicate member and duplicate attendance rate.
- Payment reconciliation variance.
- Number of failed or duplicated payment callbacks.
- Membership renewal and expiry reporting accuracy.
- Unauthorized access test pass rate (target: all defined cases denied).
- Backup restore drill success.
- Front-desk task completion rate and user-reported friction.

## 9. Acceptance criteria

A release is not accepted solely because it builds. At minimum:
- P0 workflows pass automated tests and a documented manual smoke test.
- Role and object-level authorization tests cover allowed and denied cases.
- Financial totals reconcile against transaction-level fixtures.
- Idempotency and concurrency tests cover payment/check-in retries.
- Migration and backup/restore procedures are tested in a non-production environment.
- No known critical/high security issue remains open.
- CI evidence is tied to the exact commit under review.
- Deployment, rollback, support ownership, and incident response are documented.

## 10. Open decisions

Record decisions in `Memory.md` and update this PRD when approved:
- Production hosting and database provider.
- Exact payment gateway and supported payment methods.
- Notification providers and consent model.
- Gym timezone and business-day cutoff.
- Membership pause/freeze, refund, discount, and staff override policies.
- QR token format and offline attendance conflict policy.
- Data retention, export, and member account deletion/deactivation policy.
- Whether nutrition plans are included in P1 or deferred.
