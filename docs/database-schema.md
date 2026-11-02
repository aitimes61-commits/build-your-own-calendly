# Database Schema: Initial Data Model

This is a conceptual starting point, not a final Prisma schema. Refine constraints and indexes as implementation decisions are made.

## Core entities

### User
- `id` (UUID)
- `name`
- `email` (unique)
- `timezone` (IANA identifier, e.g. `Asia/Dhaka`)
- `createdAt`, `updatedAt`

### EventType
- `id` (UUID)
- `userId` (host)
- `name`
- `slug`
- `description`
- `durationMinutes`
- `locationType`
- `isPublished`
- `minimumNoticeMinutes`
- `bookingWindowDays`
- `beforeBufferMinutes`, `afterBufferMinutes`
- `createdAt`, `updatedAt`

Suggested unique constraint: `(userId, slug)`.

### AvailabilitySchedule
- `id`
- `userId`
- `name`
- `isDefault`

### AvailabilityRule
- `id`
- `scheduleId`
- `weekday` (0–6)
- `startLocalTime`
- `endLocalTime`

A weekday can have multiple intervals. Interpret local times in the schedule owner's IANA timezone.

### DateOverride
- `id`
- `userId`
- `localDate`
- `isAvailable`
- `startLocalTime` (nullable)
- `endLocalTime` (nullable)
- `note` (optional)

Define whether overrides replace or extend normal weekly availability; this project should use one documented, consistent rule.

### Booking
- `id`
- `eventTypeId`
- `hostUserId`
- `inviteeName`
- `inviteeEmail`
- `startAt` (UTC instant)
- `endAt` (UTC instant)
- `inviteeTimezone` (IANA identifier)
- `status` (confirmed, cancelled)
- `cancellationTokenHash` (optional)
- `createdAt`, `updatedAt`

Store instants in UTC. Store timezone identifiers separately for display and future changes. Avoid storing ambiguous local timestamps as the canonical booking time.

### CalendarConnection
- `id`
- `userId`
- `provider`
- `providerAccountId`
- `encryptedAccessToken`
- `encryptedRefreshToken`
- `expiresAt`
- `syncEnabled`
- `createdAt`, `updatedAt`

### Notification
- `id`
- `bookingId`
- `type`
- `status`
- `attemptCount`
- `nextAttemptAt`
- `sentAt`
- `lastError`

## Relationships

```text
User 1---N EventType
User 1---N AvailabilitySchedule
AvailabilitySchedule 1---N AvailabilityRule
User 1---N DateOverride
EventType 1---N Booking
User 1---N CalendarConnection
Booking 1---N Notification
```

## Indexes and constraints to consider

- Unique `User.email`.
- Unique `(EventType.userId, EventType.slug)`.
- Index `Booking(hostUserId, startAt, endAt, status)`.
- Index `Booking(eventTypeId, startAt)`.
- Index `AvailabilityRule(scheduleId, weekday)`.
- Index `DateOverride(userId, localDate)`.
- Foreign keys with deliberate deletion behavior.

## Preventing overlapping bookings

A simple application-level "check then insert" is not safe under concurrency: two requests can both observe an empty slot before either inserts.

For PostgreSQL, investigate a range/exclusion constraint for active bookings per host, or use a transaction with an explicit host/slot lock and recheck. If using a database constraint, cancelled bookings must be excluded from the conflict rule. Document how buffers are represented in the conflict interval.

The exact constraint depends on product rules (host-level versus event-level conflicts, pooled resources, and multi-host meetings). Add integration tests using concurrent transactions before considering the booking engine reliable.
