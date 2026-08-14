---
description: Debug an Intranet bug on STAGING via kubectl + php artisan tinker
argument-hint: "<bug description> [--screenshot] [--staging2]"
allowed-tools: Bash(kubectl:*), Bash(/home/anga/bin/shot-latest:*), Read
---

You are debugging a bug on the **Intranet STAGING** environment.

## Target

- **Cluster context**: current kube context (verify with `kubectl config current-context`; it must reach the `intranet-staging` namespace — do not switch contexts unless it doesn't).
- **Namespace**: `intranet-staging`
- **Deployment**: `intranet-deployment` by default. If `--staging2` is in `$ARGUMENTS`, use `intranet-deployment2` instead (the staging2 env — see [[feedback_intranet_staging_branch]]; intranet features ship to staging2).
- **Container**: `intranet`
- **App path inside the container**: `/opt/effenco/intra`

This is staging — safe to probe, but still prefer read-only investigation unless the user asks you to change state.

## Bug to investigate

$ARGUMENTS

## Steps

1. **Parse `$ARGUMENTS`.** Strip the `--screenshot` and `--staging2` flags; the remainder is the bug description. Set `DEPLOY=intranet-deployment2` if `--staging2` was given, else `DEPLOY=intranet-deployment`.
   - If `--screenshot` is present: run `/home/anga/bin/shot-latest`, then use the **Read** tool on the exact path it prints to view the latest screenshot. Use it as visual evidence of the bug and fold it into your investigation.

2. **Locate the pods** (both staging deployments share the namespace; staging uses label `app=apps-intra`, staging2 uses `app=apps-intra2`):
   ```bash
   kubectl get pods -n intranet-staging                       # see everything
   kubectl get pods -n intranet-staging -l app=apps-intra2    # staging2 (use app=apps-intra for staging)
   ```
   (The `kubectl exec deployment/$DEPLOY` form below targets the right pod regardless of label.)

3. **Run Tinker non-interactively** to inspect models/data/state. Always `cd` to the app path and use `--execute`:
   ```bash
   kubectl exec deployment/$DEPLOY -n intranet-staging -c intranet -- \
     sh -c 'cd /opt/effenco/intra && php artisan tinker --execute="<PHP one-liner>"'
   ```
   Examples:
   - `App\Models\Stats\Statistic::where('name','FCAP_FUEL_LOST_L')->first()`
   - `App\Models\Vehicle\Vehicle::count()`
   - `config('app.env')`

4. **Other useful probes** (run via the same `kubectl exec ... -- sh -c '...'` pattern from `/opt/effenco/intra`):
   - `php artisan about` — framework/env summary
   - `php artisan route:list | grep -i <feature>` — confirm a route exists
   - `tail -n 200 storage/logs/laravel.log` — recent errors/stack traces
   - `php artisan migrate:status` — confirm migrations are applied

5. **Form a hypothesis** from the evidence (screenshot + tinker + logs), confirm it with one more targeted query, and report:
   - **What's happening** (observed behavior + evidence)
   - **Root cause** (the why, with file/line references in `modules/intranet` when relevant)
   - **Fix** (proposed change; apply on a branch if the user asks — intranet features merge into `staging2`)

Keep each `kubectl exec` focused and quote the command output you rely on.
