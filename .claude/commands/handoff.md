---
description: Save the current task state to a file so the session can be cleared and resumed without losing context.
---

Write `docs/plans/HANDOFF.md` (overwrite if it exists; it is a scratch file, do not commit it) containing:
- Goal of the task and the plan file it belongs to, if any
- What is done, with the commits/files involved
- What is next, in order
- Decisions made and why; dead ends already tried
- Exact commands to run the relevant tests
- Open questions for the human

Be dense and factual, no narrative. Then tell me I can run `/clear` and start the next session with: "Read docs/plans/HANDOFF.md and continue."
