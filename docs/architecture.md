# System Architecture

This document describes a maintainable architecture for an independent scheduling application.

## 1. Main components

### Web application
A Next.js application serves two distinct experiences:

- **Host dashboard:** authenticated event, availability, and booking management.
- **Public booking pages:** accessible pages where invitees view event details and request a time.

### Domain services
Keep business rules out of UI components. Suggested service boundaries:

- `EventTypeService`: create, update, publish, and archive event types.
- `AvailabilityService`: resolve weekly rules, date overrides, buffers, and busy periods.
- `BookingService`: validate and create, cancel, or reschedule bookings.
- `CalendarService`: provider OAuth, event reads/writes, and sync state.
- `NotificationService`: queue confirmation, cancellation, and reminder messages.

### Persistence
PostgreSQL stores users, event types, availability, bookings, connected calendars, and notification state. Prisma can provide typed database access and migrations.

### External services
Email and calendar providers should be adapters behind service interfaces. This keeps provider-specific logic replaceable and testable.

## 2. Request flow

### Public availability request
1. Resolve the host and published event type from the public URL.
2. Validate the requested date range and timezone.
3. Load schedule rules, date overrides, existing bookings, and cached/provider busy periods.
4. Generate candidate intervals.
5. Remove intervals that violate duration, notice, horizon, buffer, or conflict rules.
6. Return available slots with explicit timezone information.

### Booking request
1. Validate and normalize the submitted payload.
2. Authenticate the invitee only if the product requires it; public booking can also use verified email.
3. Recompute availability on the server.
4. Begin a database transaction.
5. Claim the slot using the chosen concurrency-control strategy.
6. Persist the booking and an outbox/notification record.
7. Commit.
8. Process email and calendar side effects asynchronously with retries.

Do not hold a database transaction open while waiting for a third-party API.

## 3. Suggested application layout

```text
src/
  app/
    (auth)/
    dashboard/
    book/[username]/[eventSlug]/
    api/
  components/
    booking/
    calendar/
    dashboard/
    ui/
  lib/
    auth.ts
    db.ts
    timezone.ts
  services/
    availability.service.ts
    booking.service.ts
    calendar.service.ts
    notification.service.ts
  validators/
  types/
prisma/
  schema.prisma
docs/
tests/
```

## 4. Security boundaries

- Enforce ownership checks on every host dashboard mutation.
- Public endpoints must expose only published event information and necessary availability.
- Rate-limit public availability and booking endpoints.
- Validate inputs on the server with a schema validator.
- Store OAuth refresh tokens encrypted at rest.
- Verify OAuth state and redirect URIs.
- Never expose secrets in client components or public environment variables.
- Avoid leaking invitee details through public endpoints.
- Use CSRF protections appropriate to the authentication and request model.

## 5. Reliability

Calendar providers and email services can be unavailable. Persist the booking first, then process external effects through a retryable queue or transactional outbox. Track sync status and surface failures to the host rather than silently losing them.

## 6. Observability

Log structured events with correlation IDs, such as:

- availability.requested
- booking.validation_failed
- booking.created
- booking.cancelled
- calendar.sync_failed
- notification.delivery_failed

Do not log access tokens, passwords, or unnecessary personal information.
