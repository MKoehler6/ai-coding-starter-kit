# Job-Research Tool

> A personal tool that searches job portals every night for keywords (e.g. "Informatiker", "Python"), stores the ads with company, title, link, and required skills, removes duplicates, and shows trends in a dashboard. Built with an AI-powered development workflow using specialized skills for Requirements, Architecture, Backend, Frontend, QA, and Deployment.

Origin: `Job-Recherche Tool – Brainstorming Session.md` (project root). Settled decisions are condensed in `.claude/rules/domain.md`.

## Tech Stack

- **Backend:** Python, FastAPI, SQLAlchemy, Alembic, Pydantic / pydantic-settings, uv
- **Pipeline:** Nightly run (systemd timer) — source adapters, dedup, LLM extraction, geocoding
- **LLM:** Claude API via the `anthropic` SDK (model configured via env var)
- **Database:** PostgreSQL
- **Frontend:** React, Vite, TypeScript, Tailwind CSS + shadcn/ui (copy-paste components)
- **Deployment:** Self-managed Linux server (systemd, nginx, certbot, ufw)
- **Tests:** pytest (backend), Vitest + Playwright (frontend)

## Project Structure

```
backend/
  app/
    api/          FastAPI routers (read API for the dashboard)
    models/       SQLAlchemy models
    schemas/      Pydantic schemas (API + LLM output)
    services/     Business logic (dedup, trend, skills, geocoding, llm)
    scrapers/     One adapter per job source
    jobs/         Entry points for the nightly run
  migrations/     Alembic migrations
  tests/          pytest, fixtures/ with saved source responses
frontend/
  src/
    components/ui/  shadcn/ui components (NEVER recreate these)
    components/     Feature components
    hooks/          Custom React hooks
    pages/          Dashboard views
    lib/            api.ts (typed API client), utils.ts
  e2e/            Playwright E2E tests
deploy/           systemd units/timers, nginx config, backup scripts
features/         Feature specifications (PROJ-X-name.md)
  INDEX.md        Feature status overview
docs/
  PRD.md          Product Requirements Document
  operations.md   Server operations (created by /deploy)
```

## Development Workflow

1. `/init` - Initialize the project: PRD + feature map from the brainstorming document (run once at the start)
2. `/write-spec` - Create a full feature spec for one feature
3. `/architecture` - Design tech architecture (PM-friendly, no code)
4. `/backend` - Build database schema, API, and pipeline stages
5. `/frontend` - Build dashboard UI (shadcn/ui first!)
6. `/qa` - Test against acceptance criteria + security audit
7. `/deploy` - Deploy to the Linux server + production-ready checks

Pipeline-only features skip `/frontend`; UI-only features skip `/backend`.
Use `/refine PROJ-X` at any point to revisit and improve an existing feature spec.

## Feature Tracking

All features tracked in `features/INDEX.md`. Every skill reads it at start and updates it when done. Feature specs live in `features/PROJ-X-name.md`.

## Key Conventions

- **Feature IDs:** PROJ-1, PROJ-2, etc. (sequential)
- **Commits:** `feat(PROJ-X): description`, `fix(PROJ-X): description`
- **Single Responsibility:** One feature per spec file (one job source = one feature)
- **Domain rules first:** `.claude/rules/domain.md` is binding; changes only via `/refine` with user approval
- **No captcha solving or bot-detection evasion:** blocked sources are logged and skipped
- **Schema changes only via Alembic migrations**
- **shadcn/ui first:** NEVER create custom versions of installed shadcn components
- **Human-in-the-loop:** All workflows have user approval checkpoints; server-changing commands need explicit confirmation
- **Tests:** Backend tests in `backend/tests/` (mirroring `app/`), offline with fixtures and mocks. Frontend unit tests co-located (`useHook.test.ts` next to `useHook.ts`). E2E tests in `frontend/e2e/`.

## Build & Test Commands

```bash
# Backend (in backend/)
uv sync                                  # Install dependencies
uv run uvicorn app.main:app --reload     # API dev server (localhost:8000, docs at /docs)
uv run python -m app.jobs.daily_run      # Run the nightly pipeline manually (hits live sources!)
uv run alembic upgrade head              # Apply migrations
uv run pytest                            # Unit + integration tests
uv run ruff check . && uv run ruff format --check .
uv run mypy app                          # Type check

# Frontend (in frontend/)
npm run dev          # Vite dev server (localhost:5173)
npm run build        # Production build
npm run lint         # ESLint
npm test             # Vitest unit tests
npm run test:e2e     # Playwright E2E tests
```

## Product Context

@docs/PRD.md

## Feature Overview

@features/INDEX.md
