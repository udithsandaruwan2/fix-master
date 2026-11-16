# ADR-010: Self-Hosted Nominatim for Sri Lanka Geocoding

**Status:** Accepted
**Date:** 2026-11-16
**Deciders:** human

## Context

AutoLanka needs reliable geocoding (address ↔ coordinates) for Sri Lanka:
- Forward: "123 Galle Road, Colombo 3" → [79.86, 6.92]
- Reverse: [79.86, 6.92] → "Galle Road, Colombo 3"
- Must work offline-capable (PWA)
- No API key costs; no rate limits for internal use
- Sri Lanka specific: addresses often informal, bilingual

## Decision

**Self-host Nominatim on Railway/Render** using **Sri Lanka OSM extract**.

### Deployment

| Component | Spec |
|---|---|
| **Host** | Railway/Render (same as API + Postgres) |
| **Database** | PostgreSQL 16 + PostGIS 3.4 (shared instance) |
| **Data** | Sri Lanka extract from Geofabrik (~500MB PBF → ~2GB DB) |
| **Import time** | ~30-60 min initial; ~10 min incremental |
| **API** | `https://nominatim.autolanka.lk` (internal DNS) |
| **Rate limit** | None (internal); Redis cache 24h TTL |

### Architecture

```
Frontend (Leaflet) → React-Leaflet → OSM Tiles
         │
         ├─ Geocode: /api/geo/search?q=... → API → Nominatim (cached)
         └─ Reverse: /api/geo/reverse?lat=...&lon=... → API → Nominatim (cached)
```

### API wrapper (NestJS)

```typescript
// GeoController
@Get('search')
async search(@Query('q') q: string, @Query('lang') lang = 'en') {
  const cacheKey = `geo:search:${lang}:${hash(q)}`;
  return this.cache.wrap(cacheKey, () => this.nominatim.search(q, lang), 86400);
}

@Get('reverse')
async reverse(@Query('lat') lat: number, @Query('lon') lon: number) {
  const cacheKey = `geo:reverse:${lat}:${lon}`;
  return this.cache.wrap(cacheKey, () => this.nominatim.reverse(lat, lon), 86400);
}
```

### Data freshness

- **Monthly**: Re-import Sri Lanka extract via cron
- **On-demand**: Trigger re-import via admin dashboard
- **Monitoring**: Alert if Nominatim returns 5xx or latency > 2s

## Consequences

**Positive:**
- Zero marginal cost per request
- Full control over Sri Lanka data quality
- Can add custom POIs (repair shops, tow depots) directly to OSM
- Works offline-first (cached results in Redis + SW)
- Bilingual results (Sinhala/Tamil names in OSM `name:si`, `name:ta`)

**Negative:**
- Operational burden: monitor, update, backup Nominatim DB
- Initial import time + disk space (~2GB)
- Nominatim setup complexity (Docker recommended)
- Rural address coverage depends on OSM contributors

## Mitigations

| Risk | Mitigation |
|---|---|
| Nominatim downtime | Redis cache 24h; fallback to browser `navigator.geolocation` |
| Poor rural coverage | Allow manual pin drop on map as primary input |
| Import failures | Automated health check; alert on failed cron |
| Data staleness | Monthly re-import; admin "refresh now" button |

## Alternatives considered

| Option | Rejected because |
|---|---|
| Public Nominatim (nominatim.openstreetmap.org) | Rate limited (1 req/s); no SLA; shared infrastructure |
| Mapbox Geocoding | Cost at scale; API key; vendor lock-in |
| Google Geocoding API | Expensive; API key; usage limits; TOS |
| Pelias (self-hosted) | More complex setup; heavier; Nominatim simpler for single country |
| Photon (self-hosted) | Less mature; smaller community |

## References

- Nominatim Docker: `github.com/nominatim/nominatim-docker`
- Sri Lanka extract: `download.geofabrik.de/asia/sri-lanka.html`
- Nominatim API docs: `nominatim.org/release-docs/latest/api/`
- OSM Sri Lanka community: `wiki.openstreetmap.org/wiki/Sri_Lanka`