# Development Roadmap

This roadmap deliberately builds the smallest reliable individual scheduling product before adding team and enterprise features.

## Phase 0 — Foundation
- [ ] Initialize Next.js App Router and TypeScript.
- [ ] Add Tailwind CSS and shadcn/ui.
- [ ] Configure PostgreSQL and Prisma.
- [ ] Add environment validation and example variables.
- [ ] Set up linting, formatting, and test scripts.
- [ ] Add CI for lint, typecheck, and tests.

**Exit criteria:** App starts locally, database migrations run, and CI is green.

## Phase 1 — Accounts and event types
- [ ] Implement sign-up, sign-in, and sign-out.
- [ ] Add protected dashboard routes.
- [ ] Create and edit event types.
- [ ] Add unique public slugs.
- [ ] Add event publish/unpublish state.

**Exit criteria:** A host can securely manage their own event types.

## Phase 2 — Availability
- [ ] Add host IANA timezone.
- [ ] Build weekly availability editor.
- [ ] Support multiple intervals per weekday.
- [ ] Add date-specific overrides.
- [ ] Add minimum notice, booking horizon, and buffers.
- [ ] Implement timezone-aware slot generation.

**Exit criteria:** Availability tests pass across multiple timezones and daylight-saving transitions.

## Phase 3 — Public booking
- [ ] Build public event page.
- [ ] Add date and timezone selection.
- [ ] Display available slots.
- [ ] Add invitee form and validation.
- [ ] Revalidate availability on the server.
- [ ] Prevent overlapping bookings under concurrency.
- [ ] Add idempotent booking creation.

**Exit criteria:** Two simultaneous requests cannot confirm overlapping bookings for the same host.

## Phase 4 — Booking management
- [ ] Show upcoming and past bookings.
- [ ] Add secure cancellation.
- [ ] Add rescheduling.
- [ ] Add booking status history.

**Exit criteria:** Host and invitee can complete the supported booking lifecycle.

## Phase 5 — Notifications and calendar
- [ ] Send confirmation and cancellation emails.
- [ ] Add retryable notification processing.
- [ ] Implement Google OAuth.
- [ ] Read busy calendar events.
- [ ] Create calendar events after booking.
- [ ] Track sync failures and retries.

**Exit criteria:** External provider failures do not lose confirmed bookings.

## Phase 6 — Production readiness
- [ ] Add rate limiting.
- [ ] Review authorization and data exposure.
- [ ] Add structured logs and error monitoring.
- [ ] Add end-to-end tests.
- [ ] Configure backups and migrations.
- [ ] Deploy and test production workflows.

## Phase 7 — Advanced scheduling
- [ ] Custom booking questions.
- [ ] Group events.
- [ ] Multiple hosts.
- [ ] Round-robin assignment.
- [ ] Team availability.
- [ ] Webhooks and public API.
- [ ] Payments, if required.

## Definition of done

A feature is not complete merely because its UI exists. It should include server-side validation, authorization, error states, tests, documentation, and accessibility considerations.
