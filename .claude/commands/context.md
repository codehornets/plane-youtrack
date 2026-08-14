---
description: Quick local context sync — branch, status, recent commits, and MR/PR metadata
allowed-tools: Bash(git:*), Bash(glab:*), Bash(gh:*), Bash(basename:*), Read
tags: [context, sync, orientation]
---

Run the repo-local context script if present:

```bash
bash scripts/context.sh
```

If the script cannot run, gather the same minimum set with local tools:

- `git status --short`
- `git branch --show-current`
- `git log --oneline -5`
- `glab mr view` when available, otherwise `gh pr view`

Keep the result short and factual so it can replace human-pasted status.
