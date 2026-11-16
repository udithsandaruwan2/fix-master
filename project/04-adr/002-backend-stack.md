# ADR-002: Backend Stack — NestJS + PostgreSQL + PostGIS

**Status:** Accepted
**Date:** 2026-11-16
**Deciders:** human

## Context

Need a backend stack for AutoLanka that supports:
- Geo queries (radius search, distance sorting) via PostGIS
- Modular architecture for solo builder maintainability
- TypeScript end-to-end
- WebSocket support for live tracking
- Background jobs (notifications, payouts, cleanup)
- Low operational overhead (managed hosting)

## Decision

Use **NestJS 10 + TypeScript + PostgreSQL 16 + PostGIS 3.4** deployed to **Railway/Render** (managed Postgres + Redis).

- **NestJS**: Modular monolith pattern, DI, guards/interceptors/pipes, OpenAPI/Swagger, WebSocket gateway, CLI generators
- **PostgreSQL + PostGIS**: Native geo types (`geography`), `ST_DWithin`, `ST_Distance`, GiST indexes; JSONB for flexible metadata
- **TypeORM**: Entity-first, migrations, repository pattern, PostGIS support via `typeorm-postgis`
- **Redis**: Sessions, rate limiting, BullMQ for jobs, pub/sub for WebSocket scaling

### Key modules

| Module | Responsibility |
|---|---|
| `AuthModule` | Phone OTP, JWT, roles, guards |
| `ProvidersModule` | Onboarding, services, prices, availability, verification |
| `ServicesModule` | Service catalogue, pricing rules engine |
| `DispatchModule` | Tow matching algorithm, job lifecycle, live tracking |
| `BookingsModule` | Shop quotes, slot booking |
| `PaymentsModule` | Abstraction layer, ledger, commissions |
| `ReviewsModule` | Completed-job guard, badges, disputes |
| `NotificationsModule` | SMS/email/push/WhatsApp templates + queue |
| `AdminModule` | Approval queue, live map data, rules, audit log |
| `SearchModule` | PostGIS geo queries, filters, ranking |

## Consequences

**Positive:**
- Opinionated structure reduces decision fatigue
- TypeORM + PostGIS = type-safe geo queries
- WebSocket gateway built-in (Socket.IO adapter)
- BullMQ on Redis for reliable background jobs
- OpenAPI spec auto-generated for frontend contract
- Managed hosting (Railway/Render) = zero infra ops

**Negative:**
- NestJS learning curve (mitigated by CLI + docs)
- TypeORM can generate suboptimal queries — use QueryBuilder for complex geo
- Managed Postgres costs scale with size (monitor)
- WebSocket scaling needs Redis pub/sub (extra component)

## Alternatives considered

| Option | Rejected because |
|---|---|
| FastAPI + SQLAlchemy + GeoAlchemy2 | Python async geo less mature; team expertise in TS |
| Django + DRF + GeoDjango | Heavier; less WebSocket-friendly; Python/TS context switch |
| Go + pgx + custom | More boilerplate; slower solo dev velocity |
| Supabase (PostgREST) | Less control over business logic; WebSocket via Realtime limited |

## References

- NestJS docs: WebSockets, Microservices (for future), OpenAPI
- TypeORM PostGIS extension
- Railway/Render Postgres pricing
- BullMQ patterns for job queues