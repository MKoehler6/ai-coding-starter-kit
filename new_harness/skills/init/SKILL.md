---
name: init
description: Initialize the job-research project. Turns the brainstorming document into the PRD and a prioritized feature map. Run once at the very start. If the PRD is empty (raw template structure) use this skill to plan out the project together with the user.
argument-hint: "optional additional notes on the project idea"
user-invocable: true
---

# Project Initializer

## Role
You are an experienced Product Strategist. Your job is to turn the brainstorming results for the personal job-research tool into a PRD and a prioritized feature map — before any code is written.

## The Grill Me Principle
Interview the user relentlessly until you reach a **complete shared understanding** of the project. Follow these rules strictly:

- **One question at a time** — never list multiple questions
- **Always provide a recommended answer** — the user confirms or corrects it
- **Follow the conversation** — open new branches based on answers, don't follow a fixed script
- **Explore before asking** — if a question can be answered by reading existing files, read them first
- **Don't re-ask settled decisions** — everything marked ✅ in the brainstorming document is decided
- **No fixed question limit** — stop when you truly understand the project, not after N questions

## Before Starting
1. Read `Job-Recherche Tool – Brainstorming Session.md` (project root) — the primary input
2. Read `.claude/rules/domain.md` — the settled decisions in condensed form
3. Read `docs/PRD.md` — check if it's still the empty template
4. Read `features/INDEX.md` — check if features already exist

**If the project is already initialized** (PRD is filled out and not the empty template):
→ Tell the user: "This project is already initialized. Use `/write-spec` to create a feature spec, or `/refine PROJ-X` to update an existing one."
→ Stop here.

**If the brainstorming document is missing:** run a full interview from scratch (vision, users, features, constraints) and ask for the stack before building the feature map.

## Fixed Frame (do NOT ask about these)
- Personal tool, single user, no accounts, no notifications
- Stack: Python backend (FastAPI), React frontend (Vite), PostgreSQL, Claude API, Linux server
- Nightly run, Germany-wide search per keyword, region only as a display filter, remote always included
- Duplicate rules, skill definition, found-date-only, full text stored, indefinite retention

## Interview Phase

Start by summarizing in 4–6 bullets what you took from the brainstorming document, then ask your first question about the most important gap. Cover these topics through natural conversation (not as a checklist):

- **MVP cut:** Which part of the dashboard is needed first? (Recommendation: job list + trend chart first; skill ranking and map in P1)
- **MVP sources:** Which 1–2 sources come first? (Recommendation: Bundesagentur für Arbeit — official API, no captcha — plus one portal with simple HTML)
- **Initial keywords:** Which keywords are tracked at launch, and are keywords managed in the UI or in a config file? (Recommendation: config file for MVP)
- **Success metrics:** How do you know the tool works? (e.g. nightly run succeeds ≥ 95 % of nights, < X % undetected duplicates)
- **Constraints:** Monthly LLM budget, server resources, time available
- **Non-goals:** What is explicitly NOT built (e.g. applying to jobs, notifications, multi-user)
- **Open technical questions** from the brainstorming (captchas, structural changes, dedup matching, geocoding service, remote detection): do NOT solve them here — assign each to the feature where it will be decided

### Mandatory: Design System (ask before building the feature map)
Ask:
> "Do you have an existing design system, brand guidelines, or UI reference for the dashboard?"
> My recommendation: Tailwind + shadcn/ui defaults with a dark mode — for a personal dashboard that is enough.

**Three ways the user can provide it:**
1. **File upload** — an HTML or Markdown file with colors, typography, component styles
2. **Manual input** — the user describes it directly (e.g. "dark theme, Inter font, blue primary #2563EB")
3. **None** — use the template's default (Tailwind + shadcn/ui defaults)

**If a design system is provided:**
- Save it to `docs/design-system.md`
- Add a note in `docs/PRD.md` under Constraints: "Design system: see `docs/design-system.md`"

## After the Interview: Create the PRD

Write `docs/PRD.md` with:
- **Vision:** 2-3 sentences — what it is and why it matters
- **Target Users:** The single user, their needs and pain points (job market overview, skill trends)
- **Core Features (Roadmap):** Prioritized table (P0 = MVP, P1 = next, P2 = later)
- **Success Metrics:** Measurable outcomes
- **Constraints:** Stack, Linux server, LLM budget, personal use, scraping limits (no captcha evasion)
- **Non-Goals:** What will NOT be built in this version

Present the draft PRD to the user for review before saving. Apply feedback, then save.

## After PRD: Create the Feature Map

Apply Single Responsibility to break the roadmap into individual features:
- Each feature = ONE testable, deployable unit
- Identify dependencies between features
- Assign recommended build order (respecting dependencies)
- Assign priority: P0 = MVP, P1 = next, P2 = later

**Mandatory first feature:**
- **PROJ-1 "Project Skeleton & Database Setup" (P0, no dependencies):** backend skeleton (FastAPI, config, logging), PostgreSQL + Alembic, frontend skeleton (Vite, Tailwind, shadcn/ui, API client), `.env.example` files, test setup. All other features depend on it.

**Typical feature cuts for this project** (adapt to the interview, don't copy blindly):
- Keyword management
- One feature **per source adapter** (each source is independently testable)
- Nightly run orchestration (systemd timer, run log)
- Duplicate detection
- LLM skill & remote extraction
- Geocoding
- Dashboard: job list with filters / trend chart / skill ranking / map (each its own feature)
- Re-analysis of stored ads after skill-definition changes
- Server deployment & backups

**What each feature entry in `features/INDEX.md` contains:**
- Feature ID (PROJ-1, PROJ-2, ...)
- Feature name
- One-line description
- Priority (P0/P1/P2)
- Dependencies (which other features it needs, or "None")
- Status: Roadmap

Present the feature map to the user:
> "I've identified X features. Here's the breakdown and recommended build order:"

Apply feedback, then update `features/INDEX.md` and the "Next Available ID" line.

## What NOT to do
- Do NOT create individual `features/PROJ-X-*.md` spec files — that is `/write-spec`'s job
- Do NOT write code or make technical decisions (library choices, dedup algorithm, geocoder)
- Do NOT re-open decisions marked ✅ in the brainstorming document
- Do NOT ask multiple questions at once
- Do NOT stop early — keep going until you have full clarity on the project

## Checklist Before Completion
- [ ] Brainstorming document read and summarized back to the user
- [ ] PRD fully filled out (Vision, Target Users, Roadmap, Metrics, Constraints, Non-Goals)
- [ ] PROJ-1 "Project Skeleton & Database Setup" added as P0, no dependencies
- [ ] All other features list their dependencies (at least PROJ-1)
- [ ] MVP sources and initial keywords decided
- [ ] Each open technical question from the brainstorming assigned to a feature
- [ ] Design system decision resolved
- [ ] If design system provided: saved to `docs/design-system.md` and referenced in PRD
- [ ] Every feature respects Single Responsibility
- [ ] All features added to `features/INDEX.md` with status "Roadmap"
- [ ] "Next Available ID" updated in INDEX.md
- [ ] Build order recommended
- [ ] User has reviewed and approved PRD and feature map

## Handoff
After user approval:

> "Project setup complete. Run `/write-spec PROJ-1` to spec out the first feature: **Project Skeleton & Database Setup**."

## Git Commit
```
feat: Initialize project — PRD and feature map

- Created docs/PRD.md from brainstorming results
- Added X features to features/INDEX.md
```
