# ADR-004: Git Model — Promotion Tracks

**Status:** Accepted
**Date:** 2026-11-16
**Deciders:** human

## Context

Need a git branching model for AutoLanka that:
- Separates frontend and backend deployments
- Allows independent promotion to production
- Human-gated production deploys
- Works with solo builder + coding agent workflow
- Compatible with Netlify (frontend) and Railway/Render (backend)

## Decision

Use **promotion-track git model** with 5 long-lived branches:

```
main ──────────────────────────────────────────────────→ production-ready
  ↑                    ↑                    ↑
  |                    |                    |
prod/frontend ───────┘                    |
  ↑                                       |
  |                                       |
dev/frontend ─────────────────────────────┘
  ↑
  |
feat/frontend/* (feature branches)

main ──────────────────────────────────────────────────→ production-ready
  ↑                    ↑                    ↑
  |                    |                    |
prod/backend ─────────┘                    |
  ↑                                       |
  |                                       |
dev/backend ──────────────────────────────┘
  ↑
  |
feat/backend/* (feature branches)
```

### Branch purposes

| Branch | Deploys to | Promotion trigger |
|---|---|---|
| `dev/frontend` | Netlify preview (auto) | Merge from `feat/frontend/*` |
| `dev/backend` | Railway/Render preview (auto) | Merge from `feat/backend/*` |
| `prod/frontend` | Netlify production | Human: `./scripts/promote.sh frontend --apply` |
| `prod/backend` | Railway/Render production | Human: `./scripts/promote.sh backend --apply` |
| `main` | Tagged releases only | Human: merge `prod/*` after verification |

### Rules

1. **Feature branches**: `feat/frontend/<slug>` or `feat/backend/<slug>` off respective `dev/*`
2. **No direct commits** to `dev/*`, `prod/*`, or `main`
3. **Promotion script** (`promote.sh`) does dry-run by default; `--apply` executes
4. **Human approval** required for any `prod/*` or `main` promotion
5. **No force-push, rebase, or merge** of shared branches without approval

## Consequences

**Positive:**
- Independent frontend/backend deploy cadences
- Preview environments on every feature merge
- Production deploy is explicit human decision
- Rollback = promote previous `prod/*` commit
- Clear audit trail of what went to production when

**Negative:**
- More branches to track (mitigated by `promote.sh` script)
- Feature branches may drift from `dev/*` (rebase regularly)
- Two `dev/*` branches can diverge (API contract tests catch this)

## Alternatives considered

| Option | Rejected because |
|---|---|
| Trunk-based (main + short-lived) | No independent frontend/backend deploy; no staging |
| GitFlow (develop, release/*, hotfix/*) | Too heavy for solo builder; release branches not needed |
| Single `dev` + `main` | Can't deploy frontend/backend independently |
| Environment branches (staging, prod) | Doesn't separate frontend/backend concerns |

## References

- `scripts/promote.sh` implementation
- `docs/07-git-workflow.md` detailed guide
- Netlify branch deploy configuration
- Railway/Render preview deployments