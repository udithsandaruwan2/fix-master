# ADR-003: Map Provider — OpenStreetMap / Leaflet / Nominatim

**Status:** Accepted
**Date:** 2026-11-16
**Deciders:** human

## Context

Need a map provider for AutoLanka that:
- Works in Sri Lanka with good coverage
- No API key / no usage limits / no cost
- Supports geocoding (address → coordinates)
- Supports reverse geocoding (coordinates → address)
- Works with Leaflet for interactive maps
- Can be self-hosted for reliability

## Decision

Use **OpenStreetMap tiles + Leaflet + self-hosted Nominatim** for geocoding.

### Stack

| Component | Role | Hosting |
|---|---|---|
| **OSM tiles** | Base map layer | `https://tile.openstreetmap.org` (free, attribution required) |
| **Leaflet** | Interactive map JS library | Bundled in frontend |
| **React-Leaflet** | React bindings | Bundled in frontend |
| **Nominatim** | Geocoding / reverse geocoding | Self-hosted on Railway/Render (Sri Lanka extract) |

### Self-hosted Nominatim

- Import Sri Lanka OSM extract (~500MB) → ~2GB DB
- Run on managed Postgres (same Railway/Render instance)
- API: `https://nominatim.autolanka.lk/search?q=...` and `/reverse`
- Rate limit: generous (internal only); cache results in Redis

## Consequences

**Positive:**
- Zero cost, no API keys, no vendor lock-in
- Full control over geocoding data for Sri Lanka
- Leaflet is lightweight (~40KB gzipped) and mature
- OSM has good coverage in Sri Lankan cities (Colombo, Kandy, Galle, Jaffna)
- Can add custom POIs (repair shops, tow depots) to OSM directly

**Negative:**
- Self-hosted Nominatim needs maintenance (updates, monitoring)
- OSM tile server has usage policy (no bulk download, attribution)
- Geocoding quality depends on OSM data completeness in rural areas
- No built-in routing — need separate OSRM/Valhalla if turn-by-turn needed

## Alternatives considered

| Option | Rejected because |
|---|---|
| Mapbox GL JS | Costs at scale; API key required; vendor lock-in |
| Google Maps Platform | Expensive; API key; usage limits; TOS restrictions |
| MapLibre + self-hosted tiles | More infra complexity; tile generation pipeline needed |
| Leaflet + Mapbox tiles (free tier) | Free tier too small (50k loads/mo); still vendor |

## Mitigations

- **Coverage gaps**: Seed OSM with known repair shops/tow operators in Sri Lanka
- **Nominatim downtime**: Cache geocoding results 24h in Redis; fallback to browser geolocation
- **Rural areas**: Allow manual pin drop on map as primary input; geocoding secondary
- **Updates**: Monthly OSM extract re-import via cron job

## References

- Nominatim installation guide (Docker)
- OSM Sri Lanka extract: `download.geofabrik.de/asia/sri-lanka.html`
- Leaflet best practices for mobile
- OSM tile usage policy