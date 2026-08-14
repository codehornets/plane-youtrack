# Architecture

`docs/` holds the detailed repository knowledge. This file is the short map of
the main layers and ownership boundaries.

## Layers

- `apps/`: user-facing apps and backend entrypoints.
- `packages/`: shared libraries, UI, state, and support packages.
- `docs/`: repository knowledge, plans, references, and scorecards.
- `docker-compose*.yml` and `setup.sh`: local and test environment orchestration.
- `tests/`: app-level, integration, and backend verification surfaces.

## Ownership boundaries

- Shared API and UI contracts should stay in `packages/` or `docs/`, not in ad hoc notes.
- Backend test expectations live in the Docker-based API workflow.
- Root instruction files should stay short and point inward to `docs/`.

## Deeper docs

- [docs/index.md](docs/index.md)
- [BOUNDARIES.md](BOUNDARIES.md)
- [FEATURE_REGISTRY.yaml](FEATURE_REGISTRY.yaml)
- [docs/linting.md](docs/linting.md)
- [README.md](README.md)
