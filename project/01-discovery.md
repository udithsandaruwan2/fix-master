# 01 — Discovery

**Gate G1:** draft 2026-11-16 by human.

## Problem

Drivers stranded or needing roadside help in Sri Lanka have no single place to discover nearby, vetted repair shops and tow trucks with transparent pricing. They call around, negotiate blind, and cannot compare. Shops and tow operators cannot be discovered, have no dispatch queue, and rely on phone/SMS with no tracking or trust layer.

## Evidence

| # | Source | Type | What it shows | Link |
|---|---|---|---|---|
| E1 | Product specification (user brief, 2026-11-16) | Requirement | Full feature set across 4 roles; stranded-driver flow is the core | `project/00-brief.md` |
| E2 | User stack decision | Constraint | React + Vite + NestJS + Postgres + OpenStreetMap/Leaflet | `framework.conf` |
| E3 | DevOpt framework | Baseline | Promotion-track git, context packs, gate model already in place | `AGENTS.md` |

## Current alternatives

| Segment | Today | Pain | Switching cost |
|---|---|---|---|
| Stranded driver | Phone calls, WhatsApp, word of mouth | No price transparency, no ETA, no tracking | Low — just open the site |
| Garage owner | Phone, Facebook, paper ledgers | No dispatch queue, no earnings visibility | Onboarding wizard + price list editor |
| Tow operator | Phone dispatch, SMS, manual logs | No job queue, no proof upload, no payout ledger | Provider portal + dispatch alerts |
| Admin | Spreadsheets, manual reports | No verification queue, no live map, no audit log | Admin dashboard |

## Users and jobs

| Segment | Job to be done | Trigger | Success |
|---|---|---|---|
| Guest / Customer | Find nearest repair shop or tow truck, compare prices, request service, track, pay, review | Vehicle breakdown or scheduled service need | Flow completes with no phone call |
| Repair shop / garage owner | Onboard, manage price list, hours, bays, accept bookings and quotes | Sign-up | Booking accepted and completed |
| Tow truck operator / driver | Go online/offline, receive job requests, accept/decline, navigate, update status, upload proof, see earnings | Shift start | Job dispatched, completed, proof uploaded |
| Platform admin | Verify providers, manage pricing rules, disputes, payouts, content, analytics | Onboarding / dispute | Provider approved, dispute resolved |

## Assumptions

| # | Assumption | Confidence | Impact if wrong | Test | Result |
|---|---|---|---|---|---|
| A1 | OpenStreetMap / Leaflet gives adequate coverage and geocoding for Sri Lankan cities | medium | high — fallback to Mapbox/Google needed | Render map for Kandy/Colombo, geocode a sample address | pending |
| A2 | Mock provider data is sufficient to demonstrate the full flow end-to-end | high | low — real providers are phase 2 | Seed data and run stranded-driver flow | pending |
| A3 | NestJS + PostgreSQL fits a solo builder + coding agent | high | low | Scaffold API and run migrations | pending |
| A4 | Guest checkout with phone OTP is acceptable before real payments | high | low | Implement OTP flow | pending |
| A5 | LKR is the only currency needed in v1 | high | low | Price banner renders LKR | pending |

## Constraints

- React + Vite + Netlify frontend, NestJS + PostgreSQL backend, OpenStreetMap/Leaflet
- Promotion-track git (dev/frontend, dev/backend, prod/frontend, prod/backend, main)
- GDPR-style consent, PDPA-compliant PII handling, audit logging
- Mobile-first, PWA-capable, WCAG 2.2 AA, SEO-friendly server-rendered city pages
- Multi-language ready: English, Sinhala, Tamil

## Non-goals

- Native mobile apps (phase 4)
- Real-time payments / instant payouts (phase 2)
- Insurance integration, claims handling
- Multi-country expansion beyond Sri Lanka in v1
- Physical dispatch hardware / fleet management systems

## Success metrics

- **Primary:** stranded-driver flow (search → request → track → complete → review) runs end-to-end on the web portal with no phone call
- **Guardrail:** page load stays fast on slow networks; no PII leaks in logs

## Recommendation

- [x] Proceed to product definition
- [ ] Pivot: TBD
- [ ] Stop: TBD

## Open questions

1. Payment gateway for Sri Lanka — which local wallet/card provider, if any, in v1? (default: cash on completion + card abstraction stub)
2. SMS provider for OTP and notifications — which gateway? (default: stub with Twilio-compatible interface)
3. Real provider data — do we seed mock providers only, or invite real ones in v1? (default: mock only)

**Approved by:** human on 2026-11-16