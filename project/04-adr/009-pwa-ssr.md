# ADR-009: PWA + SSR City Pages

**Status:** Accepted
**Date:** 2026-11-16
**Deciders:** human

## Context

AutoLanka needs:
- **PWA**: Offline-capable for stranded drivers (poor connectivity)
- **SSR city pages**: SEO for "tow truck Colombo", "repair shop Kandy" etc.
- **Mobile-first**: 90%+ traffic expected mobile
- **Fast on 3G**: p95 < 3s load

## Decision

**React Router v7 (SSR mode) on Netlify Edge Functions + Vite PWA plugin.**

### Architecture

| Layer | Technology | Purpose |
|---|---|---|
| **Routing** | React Router v7 | File-based routes; SSR + SPA hybrid |
| **SSR** | Netlify Edge Functions | City pages rendered at edge (V8 isolates) |
| **SPA** | Vite + Service Worker | App shell cached; offline fallback |
| **Data fetching** | TanStack Query | SSR hydration + client caching |

### Route structure

```
apps/web/src/routes/
├── _layout.tsx              # Root layout (providers, header, banner)
├── index.tsx                # Landing (SSR) — "AutoLanka — Help Near You"
├── city/
│   ├── $slug.tsx            # SSR city page — "Tow Truck Colombo"
│   └── $slug.map.tsx        # Client-only map view (hydrated)
├── search.tsx               # Client — map + list split view
├── shop/
│   └── $id.tsx              # SSR shop profile
├── tow/
│   └── request.tsx          # Client — multi-step form
├── provider/
│   └── portal.tsx           # Client — protected, no SSR
├── admin/
│   └── dashboard.tsx        # Client — protected, no SSR
└── auth/
    └── verify.tsx           # Client — OTP verification
```

### PWA config (vite-plugin-pwa)

```typescript
// vite.config.ts
VitePWA({
  registerType: 'autoUpdate',
  includeAssets: ['favicon.ico', 'apple-touch-icon.png'],
  manifest: {
    name: 'AutoLanka',
    short_name: 'AutoLanka',
    theme_color: '#0066CC',
    background_color: '#FFFFFF',
    display: 'standalone',
    icons: [...]
  },
  workbox: {
    navigateFallback: '/offline.html',
    runtimeCaching: [
      { urlPattern: /^https:\/\/tile\.openstreetmap\.org/, handler: 'CacheFirst' },
      { urlPattern: /\/api\/search/, handler: 'NetworkFirst', options: { networkTimeoutSeconds: 5 } },
      { urlPattern: /\/api\/providers/, handler: 'StaleWhileRevalidate' }
    ]
  }
})
```

### Offline strategy

| Scenario | Behavior |
|---|---|
| First visit (online) | SSR city page → SW caches app shell + API responses |
| Return visit (offline) | SW serves cached app shell → shows last-known providers |
| Request tow (offline) | Queue in IndexedDB → sync when online |
| Location denied (offline) | Manual address search works (cached city pages) |

## Consequences

**Positive:**
- Edge SSR = fast TTFB globally; no origin round-trip for city pages
- PWA = works offline; installable; push notifications ready
- React Router v7 = single routing mental model for SSR + SPA
- Netlify free tier includes Edge Functions (limited but sufficient)

**Negative:**
- Edge Functions: 100ms CPU, 50ms wall time — heavy compute must stay in API
- React Router v7 SSR still evolving; migration risk
- Service Worker debugging complexity
- Cache invalidation for provider data (use `StaleWhileRevalidate` + version header)

## Alternatives considered

| Option | Rejected because |
|---|---|
| Next.js + Vercel | Cost; Edge runtime limits; vendor lock-in |
| Astro + Netlify | Islands pattern not ideal for interactive app shell |
| Pure SPA (no SSR) | SEO critical for city pages; no SSR = no indexing |
| Remix + Cloudflare | Cloudflare Workers constraints; smaller ecosystem |

## References

- React Router v7 SSR docs
- Netlify Edge Functions limits
- Vite PWA plugin (Workbox) config
- Web.dev PWA checklist
- Google SEO guide for SPAs