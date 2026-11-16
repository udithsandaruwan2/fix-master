# 02 — Product catalog (AutoLanka)

| ID | Name | Status | Track |
|---|---|---|---|
| FEAT-001 | Location & search (geolocation, address search, pin drop, fallback) | planned | frontend |
| FEAT-002 | Vehicle selector (type, make/model/year, fuel) | planned | frontend |
| FEAT-003 | Map + list split view with 8 filters | planned | frontend |
| FEAT-004 | Location price banner (persistent, LKR, updates on location change) | planned | frontend |
| FEAT-005 | Repair shop profile pages (photos, services/prices, brands, certs, hours, reviews, distance, directions, call/WhatsApp, quote, booking) | planned | frontend |
| FEAT-006 | Tow truck request flow (pickup/drop, condition, photos, price formula, dispatch, live tracking, timeline, emergency share) | planned | frontend + backend |
| FEAT-007 | Other services (mobile mechanic, jump-start, tyre, fuel, lockout, EV charging, inspection, wash/detailing) | planned | frontend + backend |
| FEAT-008 | Guest checkout (phone OTP) + optional account (saved vehicles, history, receipts) | planned | frontend + backend |
| FEAT-009 | Payment abstraction (cash, card, local wallet) + commission/payout ledger | planned | backend |
| FEAT-010 | Ratings/reviews (completed jobs only), verification badges, report/dispute | planned | frontend + backend |
| FEAT-011 | Provider portal (onboarding wizard, service/price editor, availability calendar, requests, history, earnings/payouts) | planned | frontend + backend |
| FEAT-012 | Admin dashboard (approval queue, live map, pricing rules, catalogue, mgmt, disputes, refunds, reports, content/SEO, audit log) | planned | frontend + backend |
| FEAT-013 | Notifications (SMS, email, web push, WhatsApp) + template editor | planned | backend |
| FEAT-014 | PWA + WCAG 2.2 AA + SEO SSR city pages | planned | frontend |
| FEAT-015 | i18n (English, Sinhala, Tamil) + RTL-ready | planned | frontend |

## Not this cycle (v2+)

- FEAT-101: Native mobile apps (iOS/Android)
- FEAT-102: Real-time payments / instant payouts
- FEAT-103: Insurance integration, claims handling
- FEAT-104: Multi-country expansion
- FEAT-105: Physical dispatch hardware / fleet management

## Dependencies

| Feature | Depends on |
|---|---|
| FEAT-006 | FEAT-001, FEAT-002, FEAT-009 |
| FEAT-011 | FEAT-005, FEAT-009 |
| FEAT-012 | FEAT-005, FEAT-006, FEAT-010, FEAT-011 |
| FEAT-013 | FEAT-008, FEAT-011 |