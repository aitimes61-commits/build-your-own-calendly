# Build Your Own Calendly 📅

> Learn how to design and build an independent, Calendly-inspired scheduling platform with modern web technologies.

**Build it to understand it.** This repository is an educational, step-by-step engineering guide to the core systems behind appointment scheduling: event types, availability rules, timezone-aware slot generation, reliable bookings, calendar integrations, and notifications.

> **Project status:** Documentation and architecture blueprint. The application implementation is planned; this repository does not yet contain a complete, deployable clone.

## What you'll build

An independent scheduling app where:

- Hosts create event types such as 15-, 30-, or 60-minute meetings.
- Hosts define weekly availability and date-specific exceptions.
- Each event type has a shareable public booking page.
- Invitees choose a date, timezone, and available time.
- The server revalidates availability before confirming a booking.
- Hosts and invitees receive booking and cancellation notifications.
- Optional calendar integrations synchronize events and detect conflicts.

## Feature roadmap

| Feature | Priority | Status |
|---|---:|---|
| Authentication and user profiles | P0 | Planned |
| Event type management | P0 | Planned |
| Weekly availability rules | P0 | Planned |
| Public booking page | P0 | Planned |
| Time-slot generation | P0 | Planned |
| Booking validation and conflict prevention | P0 | Planned |
| Timezone and daylight-saving handling | P0 | Planned |
| Email confirmations | P1 | Planned |
| Google Calendar OAuth and sync | P1 | Planned |
| Cancellation and rescheduling | P1 | Planned |
| Custom booking questions | P2 | Planned |
| Team and round-robin scheduling | P2 | Planned |
| Webhooks and advanced integrations | P2 | Planned |

## Suggested technology stack

- **App:** Next.js (App Router) + TypeScript
- **UI:** Tailwind CSS + shadcn/ui
- **Database:** PostgreSQL
- **Data access:** Prisma
- **Authentication:** Better Auth (or another maintained auth provider)
- **Dates:** date-fns and date-fns-tz, with IANA timezone identifiers
- **Email:** Resend
- **Calendar integration:** Google Calendar API
- **Testing:** Vitest + Playwright
- **Deployment:** Vercel or another Node.js-compatible host

Versions and provider APIs change. Check the official documentation before installing and pin compatible versions in the lockfile.

## Architecture at a glance

```text
Browser
  ├── Host dashboard
  └── Public booking page
          |
          v
Next.js application / server actions / API routes
  ├── Authentication and authorization
  ├── Event type service
  ├── Availability service
  ├── Booking service
  ├── Calendar integration service
  └── Notification service
          |
          v
PostgreSQL  <---->  Background job / email provider
          |
          v
External calendar provider (optional)
```

The browser is responsible for presentation and user input. The server is the authority for availability and booking decisions.

## Booking lifecycle

1. A host creates an event type and configures availability.
2. The app publishes a public booking URL.
3. An invitee opens the URL and chooses a date.
4. The server calculates candidate slots using the host's schedule, event duration, buffers, existing bookings, and connected-calendar busy periods.
5. The invitee selects a slot and submits booking details.
6. The server rechecks the slot and creates the booking transactionally.
7. Confirmation notifications are queued and calendar sync is attempted.
8. The host or invitee can cancel or reschedule according to the booking policy.

**Important:** Slots shown in the UI are suggestions, not reservations. Always revalidate on the server at booking time. Design the database and transaction strategy to prevent overlapping bookings under concurrent requests.

## Getting started

This repository is currently a learning blueprint. The following setup is the intended application setup once the implementation phase begins.

### Prerequisites

- Node.js LTS
- pnpm (recommended) or npm
- PostgreSQL
- Git

### Planned local setup

```bash
git clone https://github.com/aitimes61-commits/build-your-own-calendly.git
cd build-your-own-calendly
pnpm install
cp .env.example .env
# Configure the values in .env
pnpm prisma generate
pnpm prisma migrate dev
pnpm dev
```

The commands above become runnable after the application source, package manifest, and Prisma schema are added.

## Learning path

1. Read [the architecture guide](docs/architecture.md).
2. Study [the reverse-engineering approach](docs/reverse-engineering.md).
3. Review [the data model](docs/database-schema.md).
4. Understand [the booking engine](docs/booking-engine.md).
5. Follow [the development roadmap](docs/roadmap.md).
6. Contribute using [CONTRIBUTING.md](CONTRIBUTING.md).

## Engineering principles

- Store instants in UTC; store the host's IANA timezone separately.
- Never use a browser clock as the source of truth.
- Revalidate availability on the server during booking.
- Prevent overlapping bookings at the database/transaction layer where possible.
- Make booking creation idempotent to handle retries.
- Treat external calendar sync and email delivery as retryable side effects.
- Keep OAuth tokens and API keys out of source control.
- Test daylight-saving transitions and concurrent booking attempts.

## Research references

- [Calendly Developer Portal](https://developer.calendly.com/)
- [Calendly Help Center](https://help.calendly.com/)
- [Next.js Documentation](https://nextjs.org/docs)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [Prisma Documentation](https://www.prisma.io/docs)
- [Google Calendar API](https://developers.google.com/calendar/api)
- [IANA Time Zone Database](https://www.iana.org/time-zones)

## Contributing

Issues, documentation improvements, tests, and implementation contributions are welcome. Please read [CONTRIBUTING.md](CONTRIBUTING.md) before opening a pull request.

## Disclaimer

This is an independent educational project inspired by common scheduling-product functionality. It is not affiliated with, sponsored by, or endorsed by Calendly. Calendly is a trademark of its respective owner. Do not copy proprietary source code, private APIs, protected branding, or assets. Implement functionality independently using public documentation and your own design.

## License

Released under the [MIT License](LICENSE).
