# ADR-001: Frontend Stack — React + Vite + Netlify

**Status:** Accepted
**Date:** 2026-11-16
**Deciders:** human

## Context

Need a modern, fast frontend stack for AutoLanka that supports:
- Mobile-first PWA with offline capability
- SSR for SEO-friendly city landing pages
- TypeScript for maintainability
- Low operational overhead (solo builder)
- Free/cheap hosting with global CDN

## Decision

Use **React 18 + Vite + TypeScript** deployed to **Netlify**.

- **React 18**: Concurrent features, Suspense, mature ecosystem
- **Vite**: Fast dev server, optimized builds, plugin ecosystem
- **TypeScript**: Type safety across API contracts
- **Netlify**: Edge functions for SSR, global CDN, free tier generous, form handling, deploy previews

### Key libraries

| Purpose | Choice |
|---|---|
| Routing | React Router v7 (SSR-ready) |
| State | TanStack Query (server) + Zustand (client) |
| Maps | Leaflet + React-Leaflet (OSM tiles) |
| UI primitives | Radix UI + Tailwind CSS |
| Forms | React Hook Form + Zod |
| i18n | i18next + react-i18next |
| PWA | Vite PWA plugin (Workbox) |
| Testing | Vitest + React Testing Library + Cypress E2E |

## Consequences

**Positive:**
- Zero-config SSR via Netlify Edge Functions
- Excellent DX with hot module replacement
- Strong TypeScript support
- PWA out of the box
- Free hosting with custom domains

**Negative:**
- Netlify Edge Functions have CPU/time limits (100ms/50ms) — move heavy compute to API
- React Router v7 SSR patterns still maturing
- Bundle size budget discipline needed for 3G performance

## Alternatives considered

| Option | Rejected because |
|---|---|
| Next.js + Vercel | Vercel costs at scale; Edge runtime limits; vendor lock-in |
| Remix + Cloudflare | Smaller ecosystem; Cloudflare Workers constraints |
| Astro + Netlify | Less interactive; islands pattern not ideal for app shell |
| SvelteKit | Team expertise in React; hiring pool larger |

## References

- Netlify Edge Functions docs
- Vite PWA plugin guide
- React Router v7 SSR guide