---
description: Plan a YouTrack ticket — read it, break into tasks, create a correctly-named branch off the right base
argument-hint: "<YouTrack ID, e.g. INTRA-33> [module] (defaults to current repo)"
allowed-tools: Bash(git:*), Bash(basename:*), mcp__youtrack__get_issue, mcp__youtrack__get_issue_comments, mcp__youtrack__change_issue_assignee, mcp__youtrack__get_current_user
tags: [routine, planning, branch]
---

You are running **stage 2: Plan the work** for a ticket.

Ticket ID = first token of `$ARGUMENTS`. Module = second token if given, else the current repo.

## 1. Read the ticket
- `mcp__youtrack__get_issue` + `mcp__youtrack__get_issue_comments` for the ID.
- **Workable-ticket gate (see [[feedback_youtrack_workable_tickets]]) — check BEFORE planning:**
  - If the ticket's State is `New` (untriaged column), **stop**. Do not plan or branch. Tell the user it's not ready to be worked and ask them to triage it out of New first.
  - If the ticket has **no proper description** (empty or a one-line stub with no real spec/acceptance criteria), **stop**. Ask the user to flesh it out, or to confirm explicitly that they want to proceed anyway.
- Summarize: goal, acceptance criteria, any linked issues, and open questions.

## 2. Break it down
- Propose a concrete task list (small, logical, testable steps) to satisfy the acceptance criteria.
- Call out unknowns or decisions that need the user before coding.

## 3. Create the branch off the correct base
Determine the repo via `basename $(git rev-parse --show-toplevel)`:
- **intranet** → base branch is `staging2` (intranet features ship to staging2 — see [[feedback_intranet_staging_branch]]).
- **processor** → base branch is `staging` (see [[feedback_processor_staging_branch]]).
- Other repos → ask which base to use (default `main`).

Then:
- `git fetch` the base, and branch from it. Branch name convention derives from the ticket: `INTRA-33` → prefix `intra33-` + a short kebab slug of the summary (see [[feedback_branch_youtrack_pattern]]). Confirm the proposed name with the user before creating.
- `git checkout -b <branch> <remote>/<base>` (confirm remote name first; intranet's may not be `origin`).

## 4. Assign & start
- Optionally assign the ticket to me (`change_issue_assignee`) and note that I'm starting it.
- End with the task list and the suggestion to begin coding, then `/verify-change` when ready.

Confirm branch name and base with the user before running any `git checkout -b`.
