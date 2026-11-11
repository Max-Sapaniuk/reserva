# reserva

An online booking platform for services — a learning pet project.

Professionals (barbers, trainers, tutors, massage therapists…) publish their services and schedules. Clients find a professional, see available time slots, book, and pay. Both sides get reminders.

The domain looks simple but covers most of the hard backend problems: concurrency and transactions, time zones, background jobs, payments and webhooks, realtime updates, permissions, and analytical SQL.

## Roles

- **Client** — searches, books, cancels, pays, leaves reviews.
- **Professional** — manages profile, services, schedule, and time off; sees bookings and analytics.
- **Admin** — moderation via Django Admin.

## Roadmap

**MVP**
- Email/password auth (JWT), roles
- Professional profiles and services (duration, price)
- Working hours, breaks, time off
- Time-zone-aware free slot calculation
- Booking and cancellation with no double booking (transaction + DB constraint)
- Booking lists with filters and pagination
- API docs (Swagger)

**V1**
- Google OAuth2 login
- Reminders 24h / 1h before (Celery beat) via email and Telegram
- Stripe payments (test mode) with webhooks
- Reviews and ratings
- Search with Redis caching
- Avatar uploads (S3-compatible storage)
- Rate limiting, idempotency keys
- Docker, CI/CD, deployment, Sentry

**V2**
- Realtime slot updates (Django Channels)
- Separate FastAPI notification service consuming events from a queue
- Analytics for professionals (window functions, CTEs)
- Simple React frontend
- Load testing and optimization

## Tech stack

- Python 3.13+, `uv`
- Django + Django REST Framework
- PostgreSQL, Redis
- Celery + Celery Beat
- Django Channels
- FastAPI + SQLAlchemy 2.0 + Alembic (notification service)
- pytest, pytest-django, factory_boy
- ruff, mypy, pre-commit
- Docker, docker compose, GitHub Actions, Sentry
- React (later)

## Architecture

```
[React SPA] ──HTTP/WS──> [Django + DRF + Channels] ──> [PostgreSQL]
                               │        │
                               │        └──> [Redis] (cache, broker)
                               │                 │
                               │           [Celery workers / beat]
                               │
                               └── events ──> [Notification service (FastAPI)] ──> Email / Telegram
```

## Key technical challenges

1. **Double booking** — EXCLUDE constraint + `select_for_update` + concurrency tests.
2. **Time zones** — client and professional in different zones; DST transitions.
3. **Idempotency** — retried requests or webhooks never create duplicates.
4. **Performance** — no N+1 queries, proper indexes, caching.
5. **Reliable notifications** — retries, outbox pattern.

## Non-goals

Mobile app, UI localization, complex billing, microservices for their own sake.

## Development

```bash
uv sync         # install dependencies
uv run pytest   # run tests
uv run ruff check .
uv run ruff format .
```
