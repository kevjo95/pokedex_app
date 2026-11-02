---
description: Review the current branch diff with the read-only reviewer subagent before opening a PR.
---

Use the `reviewer` subagent on the current changes (`git diff` against the base branch, plus any uncommitted work).
Summarize its Blocker / Should fix / Nit findings and its verdict. Do not fix anything yet: ask me which findings to address.
