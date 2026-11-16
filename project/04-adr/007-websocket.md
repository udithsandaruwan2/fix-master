# ADR-007: WebSocket for Live Tracking + Dispatch

**Status:** Accepted
**Date:** 2026-11-16
**Deciders:** human

## Context

AutoLanka needs real-time updates for:
- Tow truck live location tracking (customer view)
- Dispatch alerts (provider receives job offer)
- Job status timeline updates (both sides)
- Admin live map of active jobs/online trucks

Must work at scale with managed hosting.

## Decision

Use **Socket.IO via NestJS Gateway** with **Redis adapter** for horizontal scaling.

### Architecture

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  API Pod 1  │     │  API Pod 2  │     │  API Pod N  │
│  (NestJS)   │     │  (NestJS)   │     │  (NestJS)   │
│  Socket.IO  │     │  Socket.IO  │     │  Socket.IO  │
└──────┬──────┘     └──────┬──────┘     └──────┬──────┘
       │                   │                   │
       └───────────────────┼───────────────────┘
                           │
                    ┌──────▼──────┐
                    │   Redis     │
                    │  Pub/Sub    │
                    └─────────────┘
```

### Events

| Direction | Event | Payload |
|---|---|---|
| Server → Customer | `job:status` | `{ jobId, status, timestamp, location? }` |
| Server → Customer | `tow:location` | `{ jobId, lat, lng, heading, speed, timestamp }` |
| Server → Provider | `dispatch:offer` | `{ jobId, pickup, dropoff, price, expiresAt }` |
| Provider → Server | `dispatch:accept` | `{ jobId, providerId }` |
| Provider → Server | `dispatch:decline` | `{ jobId, providerId, reason }` |
| Provider → Server | `tow:location` | `{ jobId, lat, lng, heading, speed }` |
| Server → Admin | `admin:job:update` | `{ jobId, status, location, providerId }` |
| Server → Admin | `admin:provider:online` | `{ providerId, lat, lng, type }` |

### Rooms

- `job:{jobId}` — customer + assigned provider + admin
- `provider:{providerId}` — dispatch offers, direct messages
- `admin:live` — all admin dashboard connections

### Scaling

- Redis adapter broadcasts events across pods
- Sticky sessions not needed (stateless gateway)
- Connection count per pod ~10k (monitor)
- Fallback: polling endpoint for non-WS clients

## Consequences

**Positive:**
- Native NestJS integration (guards, pipes, interceptors)
- Automatic reconnection, rooms, acknowledgments
- Redis adapter = horizontal scaling without sticky sessions
- Binary protocol option for bandwidth savings
- Works on Railway/Render (WebSocket support)

**Negative:**
- WebSocket connections count against pod memory
- Need health checks that verify WS upgrade works
- Load balancer must support WebSocket (Railway/Render do)
- More complex than polling (but required for live tracking)

## Alternatives considered

| Option | Rejected because |
|---|---|
| Server-Sent Events (SSE) | No bidirectional; dispatch needs provider → server |
| Raw WebSocket (ws library) | No rooms, reconnection, acks; reinventing Socket.IO |
| Pusher / Ably | Cost at scale; vendor lock-in; self-hosted Redis is free |
| GraphQL Subscriptions | Overkill; REST + WS simpler for this use case |

## References

- NestJS WebSocket Gateway docs
- Socket.IO Redis adapter guide
- Railway/Render WebSocket configuration
- Connection scaling benchmarks