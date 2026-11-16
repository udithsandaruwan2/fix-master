# 03 — Architecture (AutoLanka)

**Gate G3:** proposed 2026-11-16 by human (pending approval).

## System shape

```
┌─────────────────────────────────────────────────────────────────┐
│                      AutoLanka System                           │
├─────────────────────────────────────────────────────────────────┤
│  apps/web (React + Vite + Netlify)                              │
│  ├── pages/           SSR city pages + SPA app shell            │
│  ├── components/      Shared UI (map, list, banner, forms)      │
│  ├── hooks/           useLocation, useVehicle, useProviders    │
│  ├── services/        API client, auth, payments, maps          │
│  ├── i18n/            en/si/ta translations                     │
│  └── pwa/             Service worker, offline cache             │
├─────────────────────────────────────────────────────────────────┤
│  apps/api (NestJS + PostgreSQL)                                 │
│  ├── modules/                                                    │
│  │   ├── auth/          Phone OTP, JWT, roles (guest/user/      │
│  │   │                   provider/admin)                        │
│  │   ├── providers/     Onboarding, services, prices,           │
│  │   │                   availability, verification              │
│  │   ├── services/      Service catalogue, pricing rules        │
│  │   ├── dispatch/      Tow matching, job lifecycle, tracking   │
│  │   ├── bookings/      Shop quotes, slot booking               │
│  │   ├── payments/      Abstraction layer, ledger, commissions  │
│  │   ├── reviews/       Completed-job guard, badges, disputes  │
│  │   ├── notifications/  SMS/email/push/WhatsApp templates      │
│  │   ├── admin/         Approval queue, live map, rules, audit  │
│  │   └── search/        PostGIS geo queries, filters, ranking  │
│  ├── common/          Guards, interceptors, pipes, exceptions   │
│  └── config/          TypeORM, Redis, Map provider, i18n        │
├─────────────────────────────────────────────────────────────────┤
│  Infrastructure                                                   │
│  ├── Netlify (web)    Edge functions for SSR city pages         │
│  ├── Railway/Render   (api) — managed Postgres + Redis          │
│  ├── PostgreSQL       PostGIS for geo; JSONB for flexible data  │
│  ├── Redis            Sessions, rate limits, pub/sub for live   │
│  ├── OpenStreetMap    Leaflet + Nominatim (self-hosted option)  │
│  └── S3-compatible    Provider docs, photos, job proofs         │
└─────────────────────────────────────────────────────────────────┘
```

## Data model (key entities)

| Entity | Key fields | Notes |
|---|---|---|
| `User` | id, phone, role, language, savedVehicles[], createdAt | Phone is unique; role enum |
| `Provider` | id, userId, type (shop/tow/mobile), status, verification, metadata | One user → one provider |
| `Service` | id, providerId, category, name, priceLKR, metadata | Price in minor units (cents) |
| `Availability` | id, providerId, dayOfWeek, openTime, closeTime, timezone | Recurring + exceptions |
| `Job` | id, customerId, providerId, type, status, priceLKR, pickup/drop GeoJSON, timeline[], proofUrls[] | Status: requested→matched→enroute→arrived→in_progress→completed→reviewed |
| `Review` | id, jobId, rating, comment, createdAt | Unique per job |
| `Payout` | id, providerId, amountLKR, status, periodStart, periodEnd | Ledger entry |
| `NotificationTemplate` | id, channel, locale, subject, body, variables | Admin-editable |

## API contract (boundaries)

- **Web ↔ API**: REST + WebSocket (live tracking, dispatch alerts)
- **Auth**: JWT in HttpOnly cookie + CSRF; phone OTP for guest
- **Geo**: All coordinates as GeoJSON Point [lng, lat]; PostGIS `ST_DWithin` for radius
- **Price**: Integer LKR cents; formula `base + (distanceKm * perKm) * vehicleMultiplier + surcharges`
- **Idempotency**: `Idempotency-Key` header on all mutating endpoints

## Decisions (ADRs)

| ID | Title | Status | Link |
|---|---|---|---|
| ADR-001 | React + Vite + Netlify for frontend | accepted | `04-adr/001-frontend-stack.md` |
| ADR-002 | NestJS + PostgreSQL + PostGIS for backend | accepted | `04-adr/002-backend-stack.md` |
| ADR-003 | OpenStreetMap / Leaflet / Nominatim for maps | accepted | `04-adr/003-map-provider.md` |
| ADR-004 | Promotion-track git (dev/frontend, dev/backend, prod/*, main) | accepted | `04-adr/004-git-model.md` |
| ADR-005 | Payment abstraction layer (cash/card/wallet) | accepted | `04-adr/005-payment-abstraction.md` |
| ADR-006 | Phone OTP + JWT for auth (guest + account) | accepted | `04-adr/006-auth.md` |
| ADR-007 | WebSocket for live tracking + dispatch | accepted | `04-adr/007-websocket.md` |
| ADR-008 | Multi-language (i18n) with Sinhala/Tamil support | accepted | `04-adr/008-i18n.md` |
| ADR-009 | PWA + SSR city pages for SEO | accepted | `04-adr/009-pwa-ssr.md` |
| ADR-010 | Self-hosted Nominatim for Sri Lanka geocoding | accepted | `04-adr/010-nominatim.md` |

## Non-functional requirements

| Category | Requirement |
|---|---|
| Performance | p95 < 3s on 3G; Lighthouse PWA > 90; API p99 < 500ms |
| Availability | 99.5% monthly; stateless API scales horizontally |
| Security | OWASP Top 10; PII encrypted at rest; audit log immutable; rate limits |
| Compliance | PDPA (Sri Lanka); GDPR-style consent; data retention policy |
| Accessibility | WCAG 2.2 AA; keyboard navigation; screen reader tested |
| Observability | Structured logs (JSON); traces (OpenTelemetry); metrics (Prometheus) |
| Disaster recovery | RPO 1hr (pg_dump); RTO 4hr; backup tested monthly |

## Risks and mitigations

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| OSM coverage gaps in Sri Lanka | medium | high | Self-host Nominatim with Sri Lanka extract; fallback to Mapbox |
| Mock providers insufficient for demo | low | medium | Seed 50+ providers across Colombo/Kandy/Galle; scripted E2E |
| NestJS learning curve for solo builder | low | medium | Use CLI generators; follow modular monolith pattern |
| WebSocket scaling on serverless | medium | medium | Use Redis pub/sub; consider managed WebSocket (Pusher/Ably) |
| Payment gateway integration delays | medium | low | Abstraction layer with stub; integrate real gateway in phase 2 |

## Success criteria for G3

1. Architecture doc + all 10 ADRs reviewed and approved
2. Data model covers all PRD entities
3. API boundaries clearly defined (REST + WS)
4. Infrastructure choices justified with cost estimates
5. Risks from discovery (A1–A5) addressed

---

**Approved by:** _______________ on _______________