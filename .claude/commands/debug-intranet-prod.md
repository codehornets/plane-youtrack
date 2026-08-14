---
description: Debug an Intranet bug on PRODUCTION via kubectl + php artisan tinker
argument-hint: "<bug description> [--screenshot]"
allowed-tools: Bash(kubectl:*), Bash(/home/anga/bin/shot-latest:*), Read
---

You are debugging a bug on the **Intranet PRODUCTION** environment.

## Target

- **Cluster context**: current kube context (verify with `kubectl config current-context`; it must reach the `intranet-production` namespace — do not switch contexts unless it doesn't).
- **Namespace**: `intranet-production`
- **Deployment**: `intranet-deployment`
- **Container**: `intranet`
- **App path inside the container**: `/opt/effenco/intra`

⚠️ **This is PRODUCTION.** Investigate read-only. Run only `SELECT`-style queries and read logs/config. Do NOT mutate data, run writes, queue jobs, clear caches, or change state unless the user explicitly tells you to in this session.

## Bug to investigate

$ARGUMENTS

## Steps

1. **Parse `$ARGUMENTS`.** Everything except the `--screenshot` flag is the bug description.
   - If `--screenshot` is present: run `/home/anga/bin/shot-latest`, then use the **Read** tool on the exact path it prints to view the latest screenshot. Use it as visual evidence of the bug (the error shown, the page, the values) and fold it into your investigation.

2. **Locate the pod** (a deployment selector picks a ready pod automatically):
   ```bash
   kubectl get pods -n intranet-production -l app=apps-intra
   ```

3. **Run Tinker non-interactively** to inspect models/data/state. Always `cd` to the app path and use `--execute`:
   ```bash
   kubectl exec deployment/intranet-deployment -n intranet-production -c intranet -- \
     sh -c 'cd /opt/effenco/intra && php artisan tinker --execute="<PHP one-liner>"'
   ```
   Examples (read-only):
   - `App\Models\Stats\Statistic::where('name','FCAP_FUEL_LOST_L')->first()`
   - `App\Models\Vehicle\Vehicle::count()`
   - `config('app.env')`

4. **Other useful read-only probes** (run via the same `kubectl exec ... -- sh -c '...'` pattern from `/opt/effenco/intra`):
   - `php artisan about` — framework/env summary
   - `php artisan route:list | grep -i <feature>` — confirm a route exists
   - `tail -n 200 storage/logs/laravel.log` — recent errors/stack traces
   - `php artisan migrate:status` — confirm migrations are applied

5. **Form a hypothesis** from the evidence (screenshot + tinker + logs), confirm it with one more targeted read-only query, and report:
   - **What's happening** (observed behavior + evidence)
   - **Root cause** (the why, with file/line references in the repo at `modules/intranet` when relevant)
   - **Fix** (proposed change — do not apply to prod; describe it or open it as a code change on a branch if asked)

Keep each `kubectl exec` focused and quote the command output you rely on.
