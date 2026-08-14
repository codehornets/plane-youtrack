---
description: Set up the Intranet browser auth session — runs the Playwright auth-setup script
allowed-tools: Bash(cd:*), Bash(node:*)
tags: [intranet, auth, browser]
---

Run the Intranet browser auth setup so subsequent browser-driven tests/repros have a logged-in session.

Execute:

```bash
cd /home/anga/workspace/effenco/tests/browser && node auth-setup.js
```

- Stream the script's output to the user — it may prompt for credentials or open a browser to complete login.
- If it exits non-zero or reports a missing dependency (e.g. Playwright not installed), surface the exact error and stop; do not retry blindly.
- On success, confirm the saved auth/storage state path it reports so the user knows the session is ready.
