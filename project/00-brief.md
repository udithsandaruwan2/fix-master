# 00 — Project brief

**Gate G0:** draft 2026-11-16 by human.

## Identity

- **Project:** AutoLanka
- **Owner:** human
- **Date:** 2026-11-16
- **One-liner:** A location-based marketplace where drivers find the nearest vehicle repair shops and tow trucks, see transparent prices, and request service — answering "is help available, how soon, and what will it cost" in seconds.

## Problem

Drivers stranded or needing roadside help in Sri Lanka have no single place to discover nearby, vetted repair shops and tow trucks with transparent pricing. They call around, negotiate blind, and cannot compare. Shops and tow operators cannot be discovered, have no dispatch queue, and rely on phone/SMS with no tracking or trust layer.

## Outcome

When this works, a driver in any Sri Lankan city can open the site, allow location (or type an address / drop a pin), pick their vehicle, and within seconds see the nearest repair shops and available tow trucks with live ETA and an upfront price estimate. We will know because a stranded-driver flow — search → request → track → complete → review — runs end-to-end without a phone call.

## Users

| Segment | Job to be done | Current workaround |
|---|---|---|
| Guest / Customer | Find nearest repair shop or tow truck, compare prices, request service, track, pay, review | Phone calls, WhatsApp, word of mouth |
| Repair shop / garage owner | Onboard, manage price list, hours, bays, accept bookings and quotes | Phone, Facebook, paper ledgers |
| Tow truck operator / driver | Go online/offline, receive job requests, accept/decline, navigate, update status, upload proof, see earnings | Phone dispatch, SMS, manual logs |
| Platform admin | Verify providers, manage pricing rules, disputes, payouts, content, analytics | Spreadsheets, manual reports |

## Scope

**In scope for v1**
- Location and search: browser geolocation, manual address search, map pin drop, graceful fallback
- Vehicle selector: type, make/model/year, fuel type
- Map + list split view with filters (service type, open now, price, rating, distance, verified, 24/7, mobile mechanic, EV-capable)
- Location price banner: persistent strip updating with detected location and typical service/tow prices
- Repair shop profile pages: photos, services/prices, brands, certifications, hours, reviews, distance, directions, call/WhatsApp, request quote, book slot
- Tow truck request flow: pickup/drop-off, vehicle condition, photos optional, upfront price (base + per-km + vehicle multiplier + surcharges), dispatch matching, live tracking, status timeline, emergency share-location
- Other services: mobile mechanic, jump-start, tyre change, fuel delivery, lockout, EV charging help, inspection, car wash/detailing
- Accounts: guest checkout with phone OTP, optional account with saved vehicles, history, receipts
- Payments: cash on completion, card, local wallet/gateway behind a payment abstraction layer; commission and payout ledger
- Ratings/reviews only from completed jobs; provider verification badges; report/dispute flow
- Provider portal: onboarding wizard with document upload, service/price list editor, availability calendar, incoming requests, job history, earnings/payout statements
- Admin dashboard: provider approval queue, live map of active jobs and online trucks, pricing rules engine, service catalogue, user/provider management, disputes, refunds, reports/analytics, content/SEO pages, audit log
- Notifications: SMS, email, web push, WhatsApp where available; all templates editable by admin

**Out of scope**
- Native mobile apps (iOS/Android) — phase 4; web is architected to support it via the same API
- Real-time payments / instant payouts — phase 2
- Insurance integration, claims handling
- Multi-country expansion beyond Sri Lanka in v1
- Physical dispatch hardware / fleet management systems

## Constraints

- **Deadline:** none stated
- **Budget:** none stated
- **Stack:** React + Vite + Netlify frontend, NestJS + PostgreSQL backend (user override from DevOpt default)
- **Compliance:** GDPR-style consent, data protection per Sri Lankan law (PDPA), PII encryption, audit logging
- **Team:** solo builder + coding agent
- **Map provider:** OpenStreetMap / Leaflet (no API key, no usage limits)
- **Currency:** LKR (Sri Lanka); multi-language ready: English, Sinhala, Tamil

## Success and stop conditions

- **Success:** stranded-driver flow (search → request → track → complete → review) runs end-to-end on the web portal with no phone call; provider onboarding, dispatch, and admin flows all functional
- **Guardrail:** page load stays fast on slow networks (mobile-first, PWA-capable); no PII leaks in logs
- **Stop if:** the user pivots away from Sri Lanka, or the map/provider integration proves infeasible within budget

## Riskiest assumption

A marketplace with no real providers will still demonstrate the full product flow end-to-end. Confidence: high. Impact if wrong: low (mock data is the fallback). Real provider integration is a phase-2 concern.

## Decision

- [x] Proceed to discovery
- [ ] Pivot: TBD
- [ ] Stop: TBD

**Approved by:** human on 2026-11-16