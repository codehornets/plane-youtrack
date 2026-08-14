---
description: Push the current branch and open a GitLab merge request against the correct target branch
argument-hint: "[extra MR title/notes] (optional)"
allowed-tools: Bash(git:*), Bash(glab:*), Bash(basename:*), mcp__youtrack__get_issue, mcp__youtrack__add_issue_comment
tags: [routine, mr, review]
---

You are running **stage 5: Code review & MR**. These repos are GitLab (use `glab`, not `gh`).

## 1. Pre-flight
- `git status -sb`; ensure work is committed (commit messages should reference the ticket, e.g. `INTRA-33: ...`). If there are uncommitted changes, show them and ask before committing.
- `git diff <base>...HEAD --stat` to preview what the MR will contain.
- Self-review the diff for obvious issues before pushing. Optionally suggest `/code-review` first.

## 2. Determine the target branch (do NOT default to main)
Identify the repo via `basename $(git rev-parse --show-toplevel)`:
- **intranet** → target `staging2` ([[feedback_intranet_staging_branch]]).
- **processor** → target `staging` ([[feedback_processor_staging_branch]]).
- Other → ask the user (default `main`).

## 3. Push & open the MR
- Confirm the remote name first (`git remote -v` — intranet's may not be `origin`).
- `git push -u <remote> HEAD`
- Create the MR with `glab mr create --target-branch <target> --title "<ID>: <summary>" --description "..." --fill`.
  - Title leads with the YouTrack ID. Description summarizes what/why, links the ticket, and lists test evidence from `/verify-change`.
  - Use `--draft` if the work isn't review-ready.

## 4. Link back
- Print the MR URL.
- Optionally add a YouTrack comment (`add_issue_comment`) on the ticket linking the MR, and note the ticket can move to In Review.

Confirm the target branch with the user before creating the MR if there's any ambiguity.
