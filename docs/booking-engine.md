# Booking Engine

The booking engine is the critical domain component. Its responsibility is to calculate candidate slots and safely turn one selected slot into a confirmed booking.

## 1. Inputs

A slot query needs:

- Host ID and event type.
- Requested date range.
- Host's IANA timezone.
- Weekly availability rules.
- Date-specific overrides.
- Event duration.
- Before/after buffer.
- Minimum notice and booking horizon.
- Existing active bookings.
- Busy intervals from connected calendars, when enabled.

## 2. Generate candidate intervals

Conceptually:

1. Resolve the requested local dates in the host's timezone.
2. Determine the effective availability intervals for each date.
3. Convert interval boundaries to instants using timezone-aware date logic.
4. Step through each interval using the configured slot increment.
5. Keep candidates whose full event duration fits.
6. Expand occupied intervals by applicable buffers.
7. Remove candidates overlapping bookings or external busy periods.
8. Apply minimum notice and maximum booking horizon.
9. Return instants plus explicit display timezone metadata.

Do not assume every local day has exactly 24 hours. Daylight-saving transitions can create shorter or longer local days.

## 3. Timezone rules

- Use IANA identifiers such as `America/New_York` or `Asia/Dhaka`, not abbreviations such as EST.
- Store booking start and end as UTC instants.
- Store the host and invitee timezone identifiers.
- Convert to local time only for schedule interpretation and display.
- Define how ambiguous or nonexistent local times during daylight-saving changes are handled.
- Test transitions using real timezone data.

## 4. Booking validation

When a user submits a selected slot:

1. Validate event type and public booking eligibility.
2. Validate invitee fields.
3. Confirm the requested start/end align with the event duration and allowed slot rules.
4. Recompute host availability and external conflicts.
5. Begin a transaction and acquire the chosen concurrency-control mechanism.
6. Recheck conflicts inside the transaction.
7. Insert the booking and a notification/outbox record.
8. Commit and return the booking confirmation.

A slot shown as available moments ago may have been booked by someone else. Return a clear conflict response and ask the invitee to choose another slot.

## 5. Idempotency and retries

Booking requests may be retried because of network failures. Accept an idempotency key for booking creation and persist its result with an appropriate uniqueness rule. A retry with the same key should not create a second booking.

## 6. External side effects

Do not call email or calendar APIs inside the database transaction. Commit the booking first, then process queued work:

- Send host and invitee confirmations.
- Create or update the external calendar event.
- Schedule reminders.
- Retry temporary provider failures with backoff.
- Record terminal failures for visibility and support.

A transactional outbox is a robust option: write the booking and an outbox event in the same transaction, then let a worker deliver the event.

## 7. Cancellation and rescheduling

Cancellation should be an explicit state transition, not a hard delete. Verify the caller is authorized using the host session or a secure, expiring, hashed cancellation token. Rescheduling should revalidate the new slot and update booking state atomically.

## 8. Essential tests

- Weekly schedule with one interval.
- Multiple intervals on one weekday.
- Full-day date override.
- Partial-day date override.
- Existing booking conflict.
- Buffer before and after an event.
- Minimum notice and booking horizon.
- Invitee in a different timezone.
- Daylight-saving spring-forward and fall-back.
- Two concurrent requests for the same slot.
- Duplicate request with the same idempotency key.
- Calendar provider timeout after booking commit.
