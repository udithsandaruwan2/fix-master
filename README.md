# fix-master

> Built with the Agent Project Framework. Start with `AGENTS.md`, then `project/STATE.md`.

## What this is

AutoLanka — a location-based marketplace where Sri Lankan drivers find the nearest vehicle repair shops and tow trucks, see transparent prices, and request service — answering "is help available, how soon, and what will it cost" in seconds.

## Status

- **Phase:** see `project/STATE.md`
- **Current milestone:** see `project/05-plan.md`
- **Next action:** see the `next_action` field in `project/STATE.md`

## For humans

1. Read `docs/00-start-here.md`.
2. Fill `AGENTS.md` §11 with stack and commands.
3. Run `./scripts/doctor.sh` after changes to the framework files.

## For agents

1. Read `AGENTS.md` fully.
2. Read `project/STATE.md`.
3. Load only the task artifact and the files it names.
4. Use skills from `.agents/skills/` on demand.

## Setup

```bash
pnpm install
pnpm dev
pnpm test
```

## Structure

| Path | Purpose |
|---|---|
| `AGENTS.md` | always-on rules and context map |
| `project/` | state, PRD, catalog, ADRs, plan, specs |
| `docs/` | process playbooks |
| `.agents/skills/` | on-demand procedures |
| `.agents/templates/` | document templates |

## License

MIT