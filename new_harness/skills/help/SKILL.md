---
name: help
description: Context-aware guide that tells you where you are in the workflow and what to do next. Use anytime you're unsure.
argument-hint: "optional question"
user-invocable: true
---

# Project Help Guide

You are a helpful project assistant for the personal job-research tool (Python backend, nightly scraping pipeline, React dashboard, PostgreSQL, Linux server). Your job is to analyze the current project state and tell the user exactly where they are and what to do next.

## When Invoked

### Step 1: Analyze Current State

Read these files to understand where the project stands:

1. **Check PRD:** Read `docs/PRD.md`
   - Is it still the empty template? → Project not initialized yet
   - Is it filled out? → Project has been set up

2. **Check Brainstorming:** Does `Job-Recherche Tool – Brainstorming Session.md` exist? It's the input for `/init`.

3. **Check Feature Index:** Read `features/INDEX.md`
   - No features listed? → No features created yet
   - Features exist? → Check their statuses

4. **Check Feature Specs:** For each feature in INDEX.md, check if:
   - Tech Design section exists (added by /architecture)
   - Implementation Notes exist (added by /backend, /frontend)
   - QA Test Results section exists (added by /qa)
   - Deployment section exists (added by /deploy)

5. **Check Codebase:** Quick scan of what's been built
   - `ls backend/app/api/ backend/app/scrapers/ backend/app/services/ 2>/dev/null` → API, sources, pipeline
   - `ls backend/migrations/versions/ 2>/dev/null` → Database migrations
   - `ls frontend/src/components/*.tsx frontend/src/pages/ 2>/dev/null` → Dashboard
   - `ls frontend/src/components/ui/ 2>/dev/null` → Installed shadcn components
   - `ls deploy/ 2>/dev/null` → Server configuration

### Step 2: Determine Next Action

**If PRD is empty template:**
> Your project hasn't been initialized yet.
> Run `/init` — it reads the brainstorming document and turns it into a PRD and feature map.

**If PRD exists but no features:**
> Your PRD is set up but no features have been created yet.
> Run `/write-spec` to create your first feature specification.

**If features exist with status "Roadmap":**
> Feature PROJ-X is on the roadmap but has no spec yet.
> Run `/write-spec PROJ-X`.

**If features exist with status "Planned" (no Tech Design):**
> Feature PROJ-X is ready for architecture design.
> Run `/architecture` for `features/PROJ-X-name.md`

**If features have Tech Design but no implementation:**
> Feature PROJ-X is ready for implementation.
> Pipeline/API part: run `/backend` first. Dashboard part: run `/frontend` afterwards.

**If features are implemented but no QA:**
> Feature PROJ-X is implemented and ready for testing.
> Run `/qa` for `features/PROJ-X-name.md`.

**If features have passed QA but aren't deployed:**
> Feature PROJ-X has passed QA and is ready for deployment.
> Run `/deploy` to deploy it to the server.

**If all features are deployed:**
> All current features are deployed! You can:
> - Run `/write-spec` to spec out a new feature (e.g. a new source)
> - Run `/refine PROJ-X` if a source broke or the skill definition should change
> - Check `docs/PRD.md` for planned features not yet specified

### Step 3: Answer User Questions

If the user asked a specific question (via arguments), answer it in the context of the current project state. Common questions:

- "What skills are available?" → List all available skills with brief descriptions
- "How do I add a new job source?" → `/write-spec` (one source = one feature) → `/architecture` → `/backend` → `/qa` → `/deploy`
- "A source stopped working" → Check the run record / `journalctl`, then `/refine` the source feature
- "How do I run the pipeline manually?" → `uv run python -m app.jobs.daily_run` in `backend/`
- "How do I customize this harness?" → Point to CLAUDE.md, `.claude/rules/`, `.claude/skills/`; settled product decisions are in `.claude/rules/domain.md`
- "What's the project structure?" → Explain the layout from `.claude/rules/general.md`
- "How do I deploy?" → Explain `/deploy` workflow and server prerequisites

## Output Format

Always respond with this structure:

### Current Project Status
_Brief summary of where the project stands_

### Features Overview
_Table of features and their current status (from INDEX.md)_

### Recommended Next Step
_The single most important thing to do next, with the exact command_

### Other Available Actions
_Other things the user could do right now_

If the user asked a specific question, answer that FIRST, then show the status overview.

## Important
- Be concise and actionable
- Always give the exact command to run
- Reference specific file paths
- Don't explain the framework architecture in detail unless asked
- Focus: "Here's where you are, here's what to do next"
