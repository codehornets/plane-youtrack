## RULES

1. Treat this as a Plane monorepo: keep changes focused and preserve the
   existing package, app, and Docker conventions.
2. Use the repo-native package manager and scripts. Prefer `pnpm` for Node
   workspace work and the documented Docker commands for backend tests.
3. For local context, prefer `bash scripts/context.sh` or the
   `.claude/commands/context.md` command before asking for copied branch or MR
   status.
4. If something is unclear, pick the smallest safe assumption and note it in
   your handoff or workpad.

6. Optimize for legibility: keep the app bootable per git worktree where possible and surface logs, metrics, and traces in a form Codex can inspect directly. Prefer ephemeral local observability stacks for task-scoped debugging, and use queryable logs or metrics sources like LogQL and PromQL when available.
