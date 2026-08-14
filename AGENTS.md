# Agent Development Guide

Read `RULES.md` first. `docs/` is the repository knowledge base and system of
record; this file is the map.

## Start here

- [ARCHITECTURE.md](ARCHITECTURE.md): top-level map of apps, packages, and test surfaces.
- [docs/index.md](docs/index.md): docs portal and knowledge-base index.
- [BOUNDARIES.md](BOUNDARIES.md): repo-level architectural and runtime boundaries.
- [FEATURE_REGISTRY.yaml](FEATURE_REGISTRY.yaml): feature inventory and ownership hints.
- [README.md](README.md): user-facing project overview.
- [apps/api/tests/RUNNING_TESTS.md](apps/api/tests/RUNNING_TESTS.md): backend test workflow.

## Commands

- `pnpm dev` - Start all dev servers (`web:3000`, `admin:3001`).
- `pnpm build` - Build all packages and apps.
- `pnpm check` - Run format, lint, and types.
- `pnpm check:lint` - OxLint across all packages.
- `pnpm check:types` - TypeScript type checking.
- `pnpm fix` - Auto-fix format and lint issues.
- `pnpm turbo run <command> --filter=<package>` - Target a specific package or app.
- `pnpm --filter=@plane/ui storybook` - Start Storybook on port 6006.

## Repository shape

- `apps/`: user-facing apps and backend services.
- `packages/`: shared libraries, UI, state, and infrastructure glue.
- `docs/`: source of truth for design, plans, references, and scorecards.
- `docker-compose*.yml`, `setup.sh`: local backend/test orchestration.

## Working rules

- Use `bash scripts/context.sh` or the repo context command for branch/status/PR context.
- Keep changes scoped to the requested surface.
- Prefer the smallest verification command that proves the change.
- Preserve legibility: logs, metrics, and traces should stay directly inspectable.
- Prefer ephemeral local observability stacks and queryable logs or metrics when debugging.
- Use `workspace:*` for internal packages and `catalog:` for external deps.
- Keep TypeScript strict, typed, and formatted with `oxfmt`.
- Keep backend test expectations aligned with the Docker-based API suite.

## Knowledge maintenance

- Update the relevant `docs/` page when behavior changes.
- Add new design notes under `docs/design-docs/`.
- Track execution work under `docs/exec-plans/`.
