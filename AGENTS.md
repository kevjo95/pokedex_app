# Pokédex

> Shared instructions for any AI coding agent (Claude Code, Codex, Cursor, Copilot, Gemini CLI).
> Keep this file under ~150 lines: write only what an agent cannot infer by reading the code.

## What this is
Fullstack Pokédex for Gen I (151 Pokémon), a portfolio project being refactored toward a job-application-grade showcase (`PLAN.md` = roadmap and per-PR status). React web app + Express API on MongoDB. Production auto-deploys from `main`, so it must keep working.

## Commands (run from the repo root; Node >= 22)
Verified on 2026-11-02 unless marked.
- Install: `npm ci` (CI uses it; `npm install` for adding deps)
- Build: `npm run build` (web build typechecks first)
- Run locally: `npm run dev` (API :5000 + web :5173; or `dev:api` / `dev:web`) (unverified: needs a MongoDB URI, see `apps/api/.env.example`)
- Seed DB: `npm run seed` (unverified: calls PokéAPI and writes to the DB in `MONGO_URI`)
- Test (all): `npm test`; with coverage: `npm run test:coverage`; names only: `npm run test:list`
- Test (single file/name): `npx vitest run --root apps/api test/app.test.ts` (same with `--root apps/web`, or pass a name fragment, per `docs/testing.md`)
- Lint: `npm run lint`; format: `npm run format` (check only: `npm run format:check`)
- Typecheck: `npm run typecheck`
- CI order (`.github/workflows/ci.yml`): lint, format:check, typecheck, test:coverage, build, plus a separate `npm audit --audit-level=high`. Run these before declaring done.

## Architecture map
- `packages/shared/` -> types shared by web and API. Types only, no build step, no runtime code.
- `apps/api/` -> Express 5 + Mongoose + zod. Flow: `src/index.ts` (bootstrap) -> `app.ts` (`createApp`) -> `routes/` -> `controllers/` (zod validation) -> `services/` (DB logic) -> `models/`. `config.ts` validates env at startup; `scripts/seed.ts` seeds.
- `apps/web/` -> React 19 + Vite + Chart.js + Framer Motion. All HTTP goes through `src/api/client.ts`. Tokens in `src/styles/tokens.css`.
- `docs/testing.md` (test strategy), `docs/adr/` (decisions), `docs/plans/` (work in flight), `PLAN.md` (roadmap).

## Conventions (only the non-obvious ones)
- Errors (API): throw `HttpError` or let `ZodError` bubble; `middleware/error-handler.ts` maps them (CLAUDE.md).
- HTTP (web): only via `src/api/client.ts` (typed, throws `ApiError`, takes an `AbortSignal`); never call `fetch` elsewhere (CLAUDE.md).
- Styling: a CSS Module per component, mobile-first, tokens from `tokens.css`. Exception: GameBoy and `BigInfoCard` stay plain CSS until PR 13 because their tests query class names (CLAUDE.md).
- Tests: API = Supertest against `createApp()` with in-memory MongoDB, call `useTestDb()` from `test/helpers.ts` at the top of each DB test file. Web = colocated `*.test.tsx`, mock `api/client` (not `fetch`) in component tests, fixtures in `src/test/`. Test names are full sentences, since `test:list` is the readable spec (`docs/testing.md`).
- Rate-limit tests send `X-Forwarded-For: <client>, <cloudflare edge>` with a distinct client IP per test (`docs/testing.md`).
- Comments: minimal, only what the code doesn't make obvious (CLAUDE.md).
- Git: one small PR per PLAN.md item; branches `feat/…`, `fix/…`, `chore/…`; Conventional Commit titles; squash-merged (PLAN.md Workflow). After each PR update PLAN.md status and CLAUDE.md.
- Formatting: Prettier + `.editorconfig`; CI fails on unformatted files.

## Deployment gotchas
- Each workspace must declare the tools its own scripts use (e.g. `typescript`): Vercel installs only `apps/web`'s dependencies. Never rely on root-hoisted packages.
- `trust proxy 2` in the API is deliberate (client -> Cloudflare -> Render LB); changing it breaks per-client rate limiting, and `app.test.ts` covers it.
- The Render API is a paid always-on instance: no keep-alive pings, no "waking up the server" UI.
- CORS allows only `CORS_ORIGIN` (comma-separated, `*` matches within one hostname label); preview deploys rely on that wildcard.
- When a PR changes Vercel/Render settings, tell the user exactly what to change and when (usually right after merge).
- Prod: web `https://pokedex-app-seven-tan.vercel.app`, API `https://pokedex-backend-o7nb.onrender.com` (check `/api/health` after deploys).

## Boundaries
- **Never**: commit secrets or `.env` files, edit generated code, disable tests to make them pass, force-push.
- **Ask first**: DB schema changes/re-seeding prod, public API/contract changes (`packages/shared` types, `/api` routes), adding or upgrading dependencies (Dependabot already handles routine bumps), Vercel/Render/CI config, anything touching auth (planned post-MVP).

## Workflow
1. Explore the code before proposing changes. Do not guess file locations.
2. Non-trivial change (new behavior, > 3 files, or cross-module): write a plan in `docs/plans/` first (`/plan`) and wait for approval.
3. Architecturally significant decision (new dependency, pattern, data store, boundary): record it in `docs/adr/` (`/adr`).
4. Make small, reviewable commits. Run the CI commands above before declaring done.
5. Definition of done: tests green, lint and format clean, PLAN.md/CLAUDE.md updated, plan checkboxes updated, ADR added if a decision was made.
