# AGENTS.md — api (AutoLanka)

> Nested instructions for `apps/api`. Keep under 40 lines. Closest file wins.

## Scope

This file applies to `apps/api`. Git track: **backend**.

## Commands

```bash
pnpm install
pnpm start:dev
pnpm test
pnpm lint
pnpm typecheck
pnpm build
```

## Local invariants

- NestJS + TypeScript + TypeORM + PostGIS. Profile: `.agents/profiles/api-node-nest.md`
- PostgreSQL + PostGIS on Railway/Render. Redis for sessions, queues, WS pub/sub.
- Branch from `dev/backend`. Promote to `prod/backend` only when asked.
- Phone OTP + JWT (HttpOnly cookies). Roles: guest, user, provider, admin.
- WebSocket (Socket.IO + Redis adapter) for live tracking + dispatch.
- Payment abstraction layer (cash + card stub).

## Differences from the root

- Do not edit `apps/web` from this worktree.

## Context

- Root: `../../AGENTS.md`
- Pack: `./scripts/context-pack.sh implement-api`
- Stack: `project/rules/stack.md`
- Architecture: `project/03-architecture.md`
- ADRs: `project/04-adr/`