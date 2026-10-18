# Product and UI Design System — Day Night GymOS

**Status:** UX baseline for implementation  
**Principle:** Fast, trustworthy gym workflows; clear information hierarchy; mobile-first member experience.

## 1. Design goals

- Front desk: minimize time and clicks for member lookup, check-in, renewal, and receipt tasks.
- Admin: show operational and financial state accurately, with visible filters and traceable actions.
- Trainer: make assigned members, workout plans, and progress easy to navigate.
- Member: provide a calm, simple view of membership, attendance, workouts, and progress.
- Accessibility: keyboard support, semantic structure, sufficient contrast, clear error messages.
- Consistency: shared components and interaction patterns across all four portals.

## 2. Visual direction

Use a modern, premium fitness-management aesthetic without making the interface look like a marketing landing page. Favor a restrained neutral base, strong readable typography, and one consistent accent color derived from the existing Day Night Gym brand. Do not invent a new logo or lock in a brand color before checking existing assets and owner approval.

Avoid:
- Excessive gradients, glassmorphism, decorative animation, and crowded dashboards.
- Color-only status communication.
- Tiny text, low-contrast muted labels, and hover-only controls.
- Destructive actions presented as ordinary primary buttons.

## 3. Layout patterns

### Desktop admin
- Persistent left navigation with portal identity and current user.
- Top bar for page title, date range/context, notifications if implemented, and account menu.
- Main content with a clear page heading, primary action, filters, then data.
- KPI cards only where each metric has a defined calculation and useful comparison period.
- Tables with pagination, sort, empty state, loading state, and error recovery.

### Front desk
- Prioritize search/check-in above secondary metrics.
- Large hit targets and keyboard-friendly workflows.
- Keep member identity and membership validity visible before confirming check-in.
- Use a side panel or focused modal for details rather than navigating through many pages.
- Clearly distinguish payment due, payment recorded, and receipt generated.

### Trainer
- Assigned-member list with search and clear status.
- Member overview with workout assignment and recent progress.
- Workout editor that supports exercise ordering, sets, reps, rest, and notes without excessive form complexity.
- Never display members outside the trainer's authorized scope.

### Member mobile
- Bottom navigation or compact mobile navigation for Home, Membership, Attendance, Workouts, Profile.
- At-a-glance membership status and expiry.
- Clear attendance history and receipt access.
- Workouts readable during exercise; avoid dense admin tables on mobile.
- Accessible progress charts with text alternatives.

## 4. Navigation baseline

`/admin`
- Overview
- Members
- Memberships & Plans
- Billing & Receipts
- Attendance
- Trainers & Staff
- Fitness
- Expenses & Reconciliation (when enabled)
- Reports
- Audit Log
- Settings

`/desk`
- Check-in
- Find Member
- Register Member
- Sell/Renew Membership
- Record Payment (permission-gated)
- Recent Activity

`/trainer`
- My Members
- Workout Plans
- Session Logs
- Progress

`/m`
- Home
- My Membership
- Attendance
- My Workouts
- Progress
- Receipts
- Profile & Settings

Navigation must be permission-aware, but API authorization remains mandatory.

## 5. Components and states

Shared components should include:
- App shell, sidebar, mobile navigation, breadcrumbs.
- Page header and primary/secondary action buttons.
- Search input with debounce and clear affordance.
- Filter bar with active-filter summary and reset.
- Accessible table/list with pagination.
- Status badge with text label and icon where helpful.
- Form fields with label, hint, validation, and error.
- Confirmation dialog for destructive/financial actions.
- Toast/inline feedback for success and failure.
- Skeleton loading, empty state, error state, retry action.
- Currency display component that formats integer paise safely.
- Date/time display component using the configured gym timezone.
- Permission-denied and session-expired states.

Every data-driven page must define:
1. Loading state.
2. Empty state.
3. Validation state.
4. API error state.
5. Permission-denied state.
6. Success/confirmation state.
7. Pagination or bounded-data behavior where relevant.

## 6. Interaction rules

- Use optimistic UI only for reversible, low-risk actions. Financial actions and attendance synchronization should wait for authoritative confirmation or show an explicit pending state.
- Disable duplicate submissions while a request is in flight, but rely on server idempotency as the real protection.
- Preserve user input when a recoverable request fails.
- Explain why an action is unavailable when doing so does not expose sensitive policy.
- Require a reason for high-impact overrides, refunds, and adjustments.
- Show a review step with amount, member, and method before recording a payment.
- Do not use a success toast until the server confirms the action.
- Motion should be subtle, respect reduced-motion preferences, and never delay critical workflows.

## 7. Content and formatting

- Use plain, action-oriented labels: “Record payment”, “Check in member”, “Renew membership”.
- Avoid ambiguous “Done” or “Process” labels.
- Show Indian rupee amounts consistently (₹) while storing integer paise.
- Use consistent date formatting and display timezone.
- Error messages should explain what happened and the next safe action without revealing internals.
- Support English first. Devanagari/PDF output must use fonts that render Hindi correctly if Hindi content is introduced.

## 8. Accessibility baseline

- Semantic headings and landmarks.
- Labels associated with inputs; errors programmatically associated with fields.
- Full keyboard operation and visible focus.
- No color-only meaning; status includes text.
- Appropriate contrast and scalable text.
- Accessible dialogs with focus management.
- Tables with meaningful headers.
- Charts include text summaries or accessible data alternatives.
- Respect reduced-motion settings.

## 9. Responsive behavior

- Test at narrow mobile, tablet, standard laptop, and wide desktop widths.
- Admin tables may use horizontal scrolling only when necessary and must preserve row identity.
- Member pages should not require horizontal scrolling.
- Front-desk primary actions should remain prominent on common desktop resolutions.
- Handle slow networks with explicit progress and retry states.

## 10. Design acceptance checklist

- Each portal's primary task is obvious without training.
- All important actions are keyboard accessible.
- Every list has loading/empty/error states.
- Financial amounts and membership status are unambiguous.
- Unauthorized records are not merely hidden in the UI; API tests verify denial.
- No fake data or fabricated KPI values in production screens.
- Screenshots/tests cover desktop front desk and mobile member portal.
- Brand assets and colors are based on approved source material.
