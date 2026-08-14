---
description: After merge, validate the change on the staging environment — smoke test + log check
argument-hint: "[what to smoke-test] (optional)"
allowed-tools: Bash(git:*), Bash(glab:*), Bash(kubectl:*), Bash(basename:*), Read
tags: [routine, staging, deploy]
---

You are running **stage 6: Deploy & validate on staging**.

## 1. Confirm it landed
- Identify the repo (`basename $(git rev-parse --show-toplevel)`) and its staging target:
  - **intranet** → `staging2`, env URL `https://intra-staging2.effenco.com` (see [[reference_intranet_environments]]).
  - **processor** → `staging`.
- Check the MR is merged (`glab mr list --state merged` or the MR URL) and the staging branch has the commit.

## 2. Smoke test the change
- For intranet: open the affected page on `intra-staging2.effenco.com` and exercise the change (use the `effenco-bug` / `shot-latest` skills for visual confirmation), or probe the backend with the `debug-intranet-staging` skill (kubectl + tinker, namespace `intranet-staging`, container `intranet`).
- Verify each acceptance criterion against the **real** staging env, not just local.

## 3. Check health
- Tail recent app logs for errors related to the change (`kubectl logs` in the staging namespace).
- For processor, watch for the silent-drop / Fargate Spot failure mode — orphans in input with no logs means Spot capacity, check CloudTrail RunTask failures (see [[project_staging_processor_capacity]]).

## 4. Report
State whether staging looks healthy and the change behaves as expected. Flag any errors or regressions. If good, the ticket is ready for QA/prod promotion — note that and suggest `/day-end` to log the work.
