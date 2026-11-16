---
project: "AutoLanka"
phase: 5-build
phase_name: "Build"
status: in_progress
updated: "2026-11-16"
owner: human
gates:
  G0: approved 2026-11-16 by human
  G1: approved 2026-11-16 by human
  G2: approved 2026-11-16 by human
  G3: approved 2026-11-16 by human
  G4: approved 2026-11-16 by human
  G5: proposed 2026-11-16 by human (pending approval)
next_action: "Start M1: Foundation + Auth + Geo (bootstrap apps/web, apps/api, auth, maps)"
---

# Project state

> Single source of truth for **where we are**. Agents read this first. Keep it under one screen.

## Current focus

AutoLanka kickoff. Phase 0 (Intake) and Phase 1 (Discovery) drafts are written. Awaiting human approval of G0 and G1 before moving to product definition (G2).

## Gate log

| Gate | Status | Date | Approved by | Notes |
|---|---|---|---|---|
| G0 Brief | proposed | 2026-11-16 | — | `00-brief.md` |
| G1 Discovery | proposed | 2026-11-16 | — | `01-discovery.md` |

## Blockers

- none

## Recent decisions

| Date | Decision | ADR |
|---|---|---|
| 2026-11-16 | Brand: AutoLanka, target country: Sri Lanka, currency: LKR | `00-brief.md` |
| 2026-11-16 | Stack: React + Vite + Netlify frontend, NestJS + PostgreSQL backend | `framework.conf` |
| 2026-11-16 | Map provider: OpenStreetMap / Leaflet (no API key) | `00-brief.md` |
| 2026-11-16 | Promotion-track git (dev/frontend, dev/backend, prod/frontend, prod/backend, main) | inherited from DevOpt framework |

## Next action

See `next_action` in the frontmatter. Update both together when the action changes.

## Links

- Brief: `00-brief.md`
- Discovery: `01-discovery.md`
- Stack: `project/rules/stack.md`
- Plan: `05-plan.md`
- Specs: `06-specs/`
- Handoffs: `handoffs/`

## Rules for updating this file

- Update at every phase change, gate approval, blocker, and handoff.
- Keep the body short; detail belongs in the artifact.
- The YAML frontmatter is machine-readable; keep keys stable.
- Never mark a gate `approved` without human approval.