# ADR-0001: Adopt an AI-assisted development workflow

- Status: proposed
- Date: YYYY-MM-DD
- Deciders: <team>

## Context
We want AI coding agents to work productively in this existing codebase without lowering quality. Agents start every session with no memory of the project, so what they need must live in the repository and be reviewed like code.

## Decision
We adopt a minimal, repo-native workflow:
- `AGENTS.md` is the single source of truth for agent instructions; `CLAUDE.md` imports it.
- Non-trivial changes start with a plan in `docs/plans/`, approved by a human before coding.
- Significant decisions are recorded as ADRs in `docs/adr/`.
- Subagents in `.claude/agents/` (planner, reviewer); shared commands in `.claude/commands/`.
- Parallel agent work happens in separate git worktrees.
- Humans own every PR: the author reads and can explain all AI-generated code.

## Alternatives considered
- **No shared conventions (each dev prompts ad hoc)**: inconsistent results, knowledge lost between sessions.
- **Heavy agent harness/orchestration**: too much to maintain for the value on a mature repo.

## Consequences
- Good: consistent agent behavior, decisions and intent are versioned and reviewable.
- Costs: `AGENTS.md` and ADRs must be kept current; stale instructions hurt more than none.
- Rules: keep `AGENTS.md` short; prune it when it grows; never store secrets in these files.
