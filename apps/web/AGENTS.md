# AGENTS.md — web (AutoLanka)

> Nested instructions for `apps/web`. Keep under 40 lines. Closest file wins.

## Scope

This file applies to `apps/web`. Git track: **frontend**.

## Commands

```bash
pnpm install
pnpm dev
pnpm test
pnpm lint
pnpm typecheck
pnpm build
```

## Local invariants

- React + Vite + TypeScript. Profile: `.agents/profiles/web-react-vite-netlify.md`
- Netlify hosting. `Netlify.env.get` in Functions. No secrets in the client bundle.
- Branch from `dev/frontend`. Promote to `prod/frontend` only when asked.
- PWA + SSR city pages (React Router v7 on Netlify Edge Functions)
- i18n: English, Sinhala, Tamil via i18next

## Differences from the root

- Do not edit `apps/api` from this worktree.

## Context

- Root: `../../AGENTS.md`
- Pack: `./scripts/context-pack.sh implement-web`
- Stack: `project/rules/stack.md`
- Architecture: `project/03-architecture.md`
- ADRs: `project/04-adr/`