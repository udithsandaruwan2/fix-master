# 05 — Implementation Plan (AutoLanka)

**Gate G4:** proposed 2026-11-16 by human (pending approval).

## Overview

Build AutoLanka in 6 milestones over ~12 weeks (solo builder + agent). Each milestone has clear acceptance criteria and produces a deployable increment.

| Milestone | Focus | Duration | Target |
|---|---|---|---|
| M1 | Foundation + Auth + Geo | 2 weeks | `dev/*` deployed, OTP login, map search |
| M2 | Provider Onboarding + Services | 2 weeks | Provider portal, service/price editor, verification |
| M3 | Stranded-Driver Flow (Tow) | 3 weeks | Search → request → dispatch → track → complete |
| M4 | Repair Shop Flow + Bookings | 2 weeks | Shop profiles, quotes, slot booking |
| M5 | Payments + Reviews + Admin | 2 weeks | Cash/card, commission ledger, reviews, admin dashboard |
| M6 | Polish + PWA + i18n + Launch | 1 week | Offline, Sinhala/Tamil, SEO city pages, prod deploy |

## Milestone breakdown

### M1: Foundation + Auth + Geo (2 weeks)

| Task | Track | Dependencies | Acceptance |
|---|---|---|---|
| T1.1 Bootstrap `apps/web` (React + Vite + TS + Netlify) | frontend | — | `npm run dev` works; Netlify preview deploys |
| T1.2 Bootstrap `apps/api` (NestJS + TypeORM + PostGIS) | backend | — | `npm run start:dev` works; migrations run |
| T1.3 Git promotion tracks + `promote.sh` | both | — | `dev/frontend`, `dev/backend` branches exist |
| T1.4 Phone OTP auth (stub SMS) + JWT + roles | backend | T1.2 | OTP request/verify → JWT cookie; role guards |
| T1.5 Auth UI (phone input, OTP verify, role redirect) | frontend | T1.1, T1.4 | Guest → home; provider → portal; admin → dashboard |
| T1.6 OpenStreetMap + Leaflet + React-Leaflet | frontend | T1.1 | Map renders; tile attribution shows |
| T1.7 Self-hosted Nominatim (Sri Lanka extract) + API wrapper | backend | T1.2 | `/api/geo/search` + `/reverse` return results |
| T1.8 Location search UI (geolocation, address, pin drop) | frontend | T1.6, T1.7 | All 3 inputs work; fallback chain works |
| T1.9 Vehicle selector (type, make/model/year, fuel) | frontend | T1.1 | Persists to localStorage; shows in request flow |

**M1 Definition of Done:** `dev/frontend` + `dev/backend` deployed; stranded driver can open site, allow location (or search), select vehicle, see map.

---

### M2: Provider Onboarding + Services (2 weeks)

| Task | Track | Dependencies | Acceptance |
|---|---|---|---|
| T2.1 Provider onboarding wizard (doc upload, details) | both | M1 | Wizard completes; provider record created |
| T2.2 Service/price list editor (CRUD, categories) | both | T2.1 | Provider manages services; prices in LKR cents |
| T2.3 Availability calendar (weekly + exceptions) | both | T2.1 | Calendar blocks unavailable slots |
| T2.4 Provider verification (admin queue, badges) | backend | T2.1 | Admin approves; badge shows on profile |
| T2.5 Provider portal dashboard (requests, history, earnings) | frontend | T2.1 | Real-time dispatch offers via WS |
| T2.6 Service catalogue + pricing rules (admin) | backend | T2.2 | Admin manages categories, base prices, multipliers |

**M2 Definition of Done:** Provider can onboard, list services/prices, set availability, receive dispatch offers on `dev/*`.

---

### M3: Stranded-Driver Flow — Tow Trucks (3 weeks)

| Task | Track | Dependencies | Acceptance |
|---|---|---|---|
| T3.1 Map + list split view with 8 filters | frontend | M1, M2 | All filters toggle; URL sync; SSR city pages |
| T3.2 Location price banner (persistent, LKR) | frontend | M1 | Banner on all pages; updates on location change |
| T3.3 Tow request flow (pickup/drop, condition, photos) | frontend | M1, M2 | Multi-step form; validation; photo upload |
| T3.4 Price formula (base + per-km × multiplier + surcharges) | backend | M2 | Unit tested; returns LKR cents |
| T3.5 Dispatch matching (nearest available, radius, type) | backend | M2 | Algorithm picks provider; offer sent via WS |
| T3.6 Dispatch accept/decline + timeout fallback | both | T3.5 | Provider accepts → job created; timeout → next |
| T3.7 Live tracking (tow location → customer map) | both | T3.6 | WS updates every 5s; customer sees marker |
| T3.8 Job status timeline (requested→matched→enroute→arrived→in_progress→completed) | both | T3.6 | Timeline updates in real-time |
| T3.9 Emergency share location (one-tap SMS/WhatsApp) | frontend | T3.7 | Generates share URL with live location |
| T3.10 Job completion + proof upload (provider) | both | T3.8 | Photos uploaded; job marked completed |

**M3 Definition of Done:** Stranded-driver flow (search → request → track → complete) runs end-to-end on `dev/*` with mock providers.

---

### M4: Repair Shop Flow + Bookings (2 weeks)

| Task | Track | Dependencies | Acceptance |
|---|---|---|---|
| T4.1 Repair shop profile page (photos, services, prices, brands, certs, hours, reviews, distance, directions, call/WhatsApp) | frontend | M2 | All sections render; data from API |
| T4.2 Request quote flow (customer → shop) | both | M2, T4.1 | Quote request creates record; shop notified |
| T4.3 Quote response (shop → customer with price) | both | T4.2 | Shop replies; customer sees quote |
| T4.4 Slot booking (calendar, confirm, reminders) | both | T4.3 | Customer books slot; both get confirmation |
| T4.5 Other services (mobile mechanic, jump-start, tyre, fuel, lockout, EV, inspection, wash) | both | M2 | Each service type has request flow |

**M4 Definition of Done:** Customer can browse shops, request quotes, book slots; shops manage quotes/bookings on `dev/*`.

---

### M5: Payments + Reviews + Admin (2 weeks)

| Task | Track | Dependencies | Acceptance |
|---|---|---|---|
| T5.1 Payment abstraction layer (cash + card stub) | backend | M3, M4 | Interface implemented; cash works; card returns stub |
| T5.2 Commission calculation + payout ledger | backend | T5.1 | Weekly payout entries; admin reconciliation report |
| T5.3 Reviews (completed jobs only) + badges + disputes | both | M3, M4 | Review create guarded; badges show; dispute → admin ticket |
| T5.4 Admin dashboard: approval queue, live map, pricing rules, catalogue, mgmt, disputes, refunds, reports, content/SEO, audit log | both | M2, M3, M4 | All sections functional; live map via WS |
| T5.5 Notifications (SMS/email/push stubs + template editor) | backend | M1 | Templates editable; test send works |

**M5 Definition of Done:** Cash payments work; commission ledger accurate; reviews gated; admin manages platform on `dev/*`.

---

### M6: Polish + PWA + i18n + Launch (1 week)

| Task | Track | Dependencies | Acceptance |
|---|---|---|---|
| T6.1 PWA (service worker, offline cache, install prompt) | frontend | M1 | Lighthouse PWA > 90; offline fallback works |
| T6.2 WCAG 2.2 AA audit + fixes | frontend | M1 | axe-core 0 violations; keyboard nav works |
| T6.3 i18n (Sinhala + Tamil translations) | both | All | All user strings translated; language switcher works |
| T6.4 SEO city pages (SSR, meta, structured data) | frontend | M3, M4 | `site:autolanka.lk` shows city pages; rich snippets |
| T6.5 Load test + optimize (3G p95 < 3s) | both | All | k6 script passes; bundle < 200KB gzipped |
| T6.6 Security audit (OWASP, PII encryption, rate limits) | backend | All | No critical findings; audit log immutable |
| T6.7 Production deploy (prod/frontend, prod/backend, main) | both | All | Human promotes via `promote.sh --apply`; smoke tests pass |

**M6 Definition of Done:** `prod/*` deployed; all acceptance criteria met; launch ready.

---

## Cross-cutting concerns

| Concern | Owner | Continuous |
|---|---|---|
| API contract (OpenAPI) | backend | Sync on every change |
| TypeScript types (shared) | both | `apps/api/src/types` → `apps/web/src/types` |
| E2E tests (Cypress) | frontend | Run on every `dev/*` deploy |
| Unit tests (Vitest/Jest) | both | >80% coverage on business logic |
| Database migrations | backend | Reviewed before merge |
| Token audit (`token-audit.sh`) | both | Every PR |
| Graph update (`graphify update .`) | both | After code edits |

---

## Risk mitigation

| Risk | Mitigation |
|---|---|
| Nominatim coverage gaps | Seed OSM with known providers; manual pin drop primary |
| WebSocket scaling | Redis pub/sub from day 1; monitor connection count |
| Payment gateway delay | Abstraction layer with cash + stub; real gateway phase 2 |
| Solo builder burnout | 2-week milestones; demo at end of each; scope cut if needed |
| i18n scope creep | English-first; translate only user-facing strings; automate CSV workflow |

---

## Success criteria for G4

1. Plan reviewed and approved by human
2. All 30+ tasks have clear acceptance criteria
3. Dependencies mapped; no circular deps
4. Milestones timeboxed with DoD
5. Risks from discovery (A1–A5) addressed in tasks

---

**Approved by:** _______________ on _______________