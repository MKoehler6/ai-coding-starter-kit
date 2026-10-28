# General Project Rules

## Project Context
This is a **personal job-research tool** (not a commercial product): a nightly agent searches job portals Germany-wide for keywords, stores the ads, extracts hard skills with an LLM, removes duplicates, and a dashboard shows trends.

**Stack (fixed):** Python backend (FastAPI + SQLAlchemy + Alembic), React frontend (Vite + TypeScript + Tailwind + shadcn/ui), PostgreSQL, Claude API for LLM analysis, deployment on a self-managed Linux server (systemd, nginx).

**Origin document:** `Job-Recherche Tool – Brainstorming Session.md` in the project root holds the brainstorming results. Decisions marked ✅ there are settled (summarized in `.claude/rules/domain.md`). Do not re-litigate them — only `/refine` may change them, with explicit user approval.

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
  tests/          pytest (unit + integration), fixtures/ with saved HTML
frontend/
  src/
    components/ui/  shadcn/ui components (NEVER recreate these)
    components/     Feature components
    hooks/          Custom React hooks
    pages/          Dashboard views
    lib/            api.ts (typed API client), utils.ts
  e2e/            Playwright E2E tests
deploy/           systemd units/timers, nginx config, backup scripts
features/         Feature specifications (PROJ-X-name.md) + INDEX.md
docs/             PRD.md, design-system.md, operations docs
```
If the actual structure differs, the code on disk wins — verify with `ls` / `git ls-files` before assuming paths.

## Commands
```bash
# Backend (run in backend/)
uv run uvicorn app.main:app --reload     # API dev server (localhost:8000)
uv run python -m app.jobs.daily_run      # Run the nightly pipeline manually
uv run alembic upgrade head              # Apply migrations
uv run pytest                            # Unit + integration tests
uv run ruff check . && uv run ruff format --check .
uv run mypy app                          # Type check

# Frontend (run in frontend/)
npm run dev        # Vite dev server (localhost:5173)
npm run build      # Production build (includes tsc)
npm run lint       # ESLint
npm test           # Vitest
npm run test:e2e   # Playwright
```
If `pyproject.toml` or `package.json` define different commands, use those.

## New Project Detection (MANDATORY)
Before starting ANY work, check if the project has been initialized:
1. Read `docs/PRD.md` - if it still contains placeholder text like "_Describe what you are building_", the project is NOT initialized
2. Read `features/INDEX.md` - if the features table is empty, no features have been defined

**If the project is not initialized:**
- Do NOT write any code or create any components
- Do NOT skip ahead to implementation
- Instead, tell the user: "This project hasn't been set up yet. Let's start by defining what you want to build. Run `/init` — the brainstorming document will be used as input."
- If the user already described their idea in the current message, run `/init` automatically with their description

**If the project is initialized but the user requests a feature not yet in INDEX.md:**
- Guide them to run `/write-spec` first to create the feature spec before any implementation

## Feature Tracking
- All features are tracked in `features/INDEX.md` - read it before starting any work
- Feature specs live in `features/PROJ-X-feature-name.md`
- Feature IDs are sequential: check INDEX.md for the next available number
- One feature per spec file (Single Responsibility)
- Never combine multiple independent functionalities in one spec

## Git Conventions
- Commit format: `type(PROJ-X): description`
- Types: feat, fix, refactor, test, docs, deploy, chore
- Check existing features before creating new ones: `ls features/ | grep PROJ-`
- Check existing components before building: `git ls-files frontend/src/components/`
- Check existing API routes before building: `git ls-files backend/app/api/`
- Check existing scrapers before building: `git ls-files backend/app/scrapers/`

## Human-in-the-Loop
- Always ask for user approval before finalizing deliverables
- Present options using clear choices rather than open-ended questions
- Never proceed to the next workflow phase without user confirmation
- Commands that change the production server (systemd, nginx, database on the server) always need explicit confirmation

## Status Updates (MANDATORY - Write-Then-Verify)
After completing work on any feature, you MUST update tracking files. Follow this exact sequence:

1. **Read** the feature spec (`features/PROJ-X-*.md`) and `features/INDEX.md` BEFORE editing
2. **Write** your changes using the Edit tool — do NOT just describe what you would write
3. **Re-read** the file AFTER editing to verify the changes are actually present
4. **If changes are missing**, repeat step 2 — never claim updates were made without verifying

**What to update in the feature spec:**
- Status field in the header (Planned → In Progress → In Review → Deployed)
- Implementation notes: what was built, what changed, any deviations from the original spec
- Bug fixes or design changes discovered during implementation

**What to update in `features/INDEX.md`:**
- Feature status column must match the feature spec header
- Valid statuses: Roadmap → Planned → Architected → In Progress → In Review → Approved → Deployed
  - **Roadmap**: after `/init` — feature identified, no spec file yet
  - **Planned**: after `/write-spec`
  - **Architected**: after `/architecture`
  - **In Progress**: after `/backend` or `/frontend` starts
  - **In Review**: after `/qa` starts
  - **Approved**: after `/qa` passes (no critical/high bugs)
  - **Deployed**: after `/deploy`

**NEVER do this:**
- Do NOT say "I've updated the feature spec" without actually calling the Edit tool
- Do NOT summarize changes in chat as a substitute for writing them to the file
- Do NOT skip updates because "it's obvious" or "minor"

## File Handling
- ALWAYS read a file before modifying it - never assume contents from memory
- After context compaction, re-read files before continuing work
- When unsure about current project state, read `features/INDEX.md` first
- Run `git diff` to verify what has already been changed in this session
- Never guess at import paths, module names, or API routes - verify by reading

## Handoffs Between Skills
- After completing a skill, suggest the next skill to the user
- Format: "Next step: Run `/skillname` to [action]"
- Default build order per feature: `/backend` (data, API, pipeline) → `/frontend` (dashboard UI) → `/qa` → `/deploy`
- Pipeline-only features (no UI) skip `/frontend`; UI-only features skip `/backend`
- Handoffs are always user-initiated, never automatic
