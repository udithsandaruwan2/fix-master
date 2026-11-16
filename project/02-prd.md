# 02 — PRD (AutoLanka)

**Gate G2:** proposed 2026-11-16 by human (pending approval).

## Summary

AutoLanka is a location-based marketplace where Sri Lankan drivers find the nearest vehicle repair shops and tow trucks, see transparent prices, and request service — answering "is help available, how soon, and what will it cost" in seconds. The core stranded-driver flow (search → request → track → complete → review) runs end-to-end on the web portal with no phone call.

## Primary metric

**End-to-end stranded-driver flow completion rate** — percentage of sessions that reach "review submitted" without a phone call. Guardrail: p95 page load < 3s on 3G; zero PII in logs.

## Requirements

| ID | Requirement | Test |
|---|---|---|
| REQ-001 | Browser geolocation + manual address search + map pin drop with graceful fallback | Cypress: location denied → address input works; pin drop updates results |
| REQ-002 | Vehicle selector (type, make/model/year, fuel) persists across session | localStorage round-trip; selector shows in request flow |
| REQ-003 | Map + list split view with filters (service, open now, price, rating, distance, verified, 24/7, mobile, EV) | All 8 filters toggle; URL reflects filter state; SSR city pages render |
| REQ-004 | Location price banner persists and updates with detected location | Banner visible on all pages; updates on location change; shows LKR |
| REQ-005 | Repair shop profile: photos, services/prices, brands, certs, hours, reviews, distance, directions, call/WhatsApp, request quote, book slot | Cypress: each section renders; quote request creates DB record |
| REQ-006 | Tow truck request: pickup/drop-off, vehicle condition, photos, upfront price (base + per-km + multiplier + surcharges), dispatch matching, live tracking, status timeline, emergency share | E2E: request → dispatch → track → complete; price formula unit tested |
| REQ-007 | Other services: mobile mechanic, jump-start, tyre, fuel, lockout, EV charging, inspection, wash/detailing | Each service type has provider filter and request flow |
| REQ-008 | Guest checkout with phone OTP; optional account with saved vehicles, history, receipts | OTP flow completes; account creation links history |
| REQ-009 | Payments: cash on completion, card, local wallet behind abstraction layer; commission + payout ledger | Payment adapter interface tested; ledger entries on completion |
| REQ-010 | Ratings/reviews only from completed jobs; verification badges; report/dispute flow | Review create guarded by job.completedAt; dispute creates admin ticket |
| REQ-011 | Provider portal: onboarding wizard (doc upload), service/price editor, availability calendar, incoming requests, job history, earnings/payout statements | Cypress: wizard completes; calendar blocks unavailable slots |
| REQ-012 | Admin dashboard: provider approval queue, live map of active jobs/online trucks, pricing rules engine, service catalogue, user/provider mgmt, disputes, refunds, reports/analytics, content/SEO pages, audit log | Each section loads; live map updates via WebSocket; audit log immutable |
| REQ-013 | Notifications: SMS, email, web push, WhatsApp (where available); all templates editable by admin | Template editor saves; preview renders; provider receives test notification |
| REQ-014 | Mobile-first, PWA-capable, WCAG 2.2 AA, SEO-friendly SSR city pages | Lighthouse PWA score > 90; axe-core 0 violations; city pages indexed |
| REQ-015 | Multi-language: English, Sinhala, Tamil with RTL-ready layout for future | i18n keys for all user-facing strings; language switcher persists |

## Out of scope (v1)

- Native mobile apps (iOS/Android) — phase 4
- Real-time payments / instant payouts — phase 2
- Insurance integration, claims handling
- Multi-country expansion beyond Sri Lanka
- Physical dispatch hardware / fleet management systems

## Acceptance criteria for G2

1. PRD and product catalog reviewed and approved by human
2. All requirements have a testable definition (see test column)
3. No orphan requirements — each maps to a feature in the catalog
4. Risks from discovery (A1–A5) have mitigation notes

## Success and stop conditions

- **Success:** G2 approved; architecture (G3) can begin
- **Stop if:** human pivots away from Sri Lanka or map/provider integration proves infeasible

---

**Approved by:** _______________ on _______________