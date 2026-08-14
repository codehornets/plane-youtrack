---
description: Promote a validated change to PRODUCTION — gated, confirm-before-every-step release + post-deploy verification
argument-hint: "[YouTrack ID or what's being released] (optional)"
allowed-tools: Bash(git:*), Bash(glab:*), Bash(kubectl:*), Bash(basename:*), Read, mcp__youtrack__get_issue, mcp__youtrack__update_issue, mcp__youtrack__add_issue_comment
tags: [routine, prod, release, deploy]
---

You are running **stage 7: Production release**. This is the highest-risk command — **treat every outward action as requiring explicit confirmation**. Default to read-only investigation; never push to prod, merge, or restart anything without the user saying go.

## 1. Pre-flight gate (must pass before anything)
- Identify the repo: `basename $(git rev-parse --show-toplevel)`.
- Confirm the change is already validated on staging (the `/ship-staging` step passed). If you can't confirm, stop and ask.
- Confirm the **prod branch / release mechanism** with the user — do NOT assume. Ask which applies:
  - a prod/`production`/`main` branch the staging branch merges into, **or**
  - a tag/release pipeline, **or**
  - a CI/CD promotion in GitLab.
  Per memory, staging branches are intranet→`staging2`, processor→`staging`; the prod target is not recorded, so always confirm it.
- Check for a release window / approval requirement and ask the user to confirm sign-off.

## 2. Show exactly what will ship
- `git log <prod-target>..<staging-branch> --oneline` and `git diff <prod-target>..<staging-branch> --stat`.
- Summarize: which commits/tickets, scope, and any DB migrations or config changes that need attention.
- Present this and **wait for explicit "yes, release"** before proceeding.

## 3. Execute the release (only after confirmation)
- Perform the agreed mechanism (merge staging→prod via `glab mr create --target-branch <prod>` and merge, or push a tag, or trigger the pipeline). Echo each command before running it.
- Do not bypass CI; let the pipeline run.

## 4. Post-deploy verification
- Verify on the real prod env:
  - intranet → `https://intra.effenco.io` (see [[reference_intranet_environments]]); probe backend with the `debug-intranet-prod` skill (kubectl + tinker against the prod namespace) — read-only.
  - processor → confirm the deployed image/version and watch for the silent-drop / Fargate Spot failure mode ([[project_staging_processor_capacity]]).
- Tail prod logs for errors tied to the change. Smoke-test the key acceptance criteria.

## 5. Close out & rollback readiness
- Update the YouTrack ticket to Done/Released and `add_issue_comment` with the release summary + verification evidence.
- State the rollback path explicitly (revert commit / redeploy previous tag) in case of regression, so it's ready if needed.
- If anything looks wrong, **stop and recommend rollback** rather than pushing fixes forward under pressure.
