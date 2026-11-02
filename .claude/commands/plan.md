---
description: Create an implementation plan in docs/plans/ for a task, then stop for approval.
argument-hint: <what you want to build or change>
---

Task: $ARGUMENTS

Use the `planner` subagent to produce a plan in `docs/plans/` following `docs/plans/TEMPLATE.md`.
Then show me the plan summary and the open questions, and STOP. Do not write code until I approve.
After approval, implement one step at a time, ticking the checkbox in the plan file as each step passes its test.
