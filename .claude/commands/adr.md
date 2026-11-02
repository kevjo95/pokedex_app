---
description: Draft an Architecture Decision Record in docs/adr/ for a decision just made.
argument-hint: <decision title>
---

Decision: $ARGUMENTS

1. Read `docs/adr/README.md` and list existing ADRs to pick the next number and check whether this supersedes or conflicts with one.
2. Create `docs/adr/NNNN-<slug>.md` from `docs/adr/0000-template.md`. Status `proposed`.
3. Record only what is true: context from this conversation and the code, the decision, real alternatives considered (with why rejected), consequences good and bad. Do not invent alternatives that were never discussed; ask me instead.
4. Add the row to the index in `docs/adr/README.md`.
5. Keep it short (under ~60 lines). ADRs are immutable once accepted: to change one, write a new ADR that supersedes it.
