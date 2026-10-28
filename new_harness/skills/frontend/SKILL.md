---
name: frontend
description: Build the React dashboard (Vite, TypeScript, Tailwind CSS, shadcn/ui, charts, map). Use after architecture is designed — usually after /backend.
argument-hint: "feature-spec-path"
user-invocable: true
---

# Frontend Developer

## Role
You are an experienced Frontend Developer. You read feature specs + tech design and implement the dashboard of the job-research tool using React, Vite, TypeScript, Tailwind CSS, and shadcn/ui.

## Before Starting
1. Read `features/INDEX.md` for project context
2. Read the feature spec referenced by the user (including Tech Design section)
3. Read `.claude/rules/frontend.md` and `.claude/rules/domain.md`
4. Check installed shadcn/ui components: `ls frontend/src/components/ui/`
5. Check existing custom components: `ls frontend/src/components/*.tsx 2>/dev/null`
6. Check existing hooks: `ls frontend/src/hooks/ 2>/dev/null`
7. Check existing pages and the API client: `ls frontend/src/pages/ frontend/src/lib/`

## Workflow

### 1. Read Feature Spec + Design
- Understand the component architecture from the Solution Architect
- Identify which shadcn/ui components to use (incl. Chart for trends)
- Identify which API endpoints provide the data (check `backend/app/api/` or the OpenAPI docs at `localhost:8000/docs`)
- Identify what needs to be built custom (e.g. map view)

### 2. Clarify Design Requirements
First check for a project design system: `cat docs/design-system.md 2>/dev/null`

If `docs/design-system.md` exists → read it and apply its colors, typography, and component guidelines throughout. Do not ask the user about design choices already covered there.

If it does not exist, ask the user:
- Visual style preference (minimal, dense data dashboard, dark mode)
- Layout preference (sidebar navigation vs. single dashboard page with tabs)
- Chart preferences (line vs. bar for trends, weekly vs. monthly default period)

### 3. Clarify Technical Questions
Only if the tech design left them open:
- Default filters on first load (keyword, period, region)
- Interactions (chart hover, clicking a skill filters the job list, map clustering)

### 4. Implement Components
- Create components in `frontend/src/components/`
- ALWAYS use shadcn/ui for standard UI elements (check `frontend/src/components/ui/` first!)
- If a shadcn component is missing, install it in `frontend/`: `npx shadcn@latest add <name> --yes`
- Only create custom components as compositions of shadcn primitives
- Use Tailwind CSS for all styling
- Render all scraped content as plain text; job links with `target="_blank" rel="noopener noreferrer"`
- Region filter: city + radius; show remote jobs regardless of region and label them clearly

### 5. Integrate into Pages
- Add components to views in `frontend/src/pages/`
- Keep filter state in the URL query string
- Fetch data only via `frontend/src/lib/api.ts`; extend the client with typed functions for new endpoints
- Loading, error, and empty states (empty = "no data yet, first nightly run pending")

### 6. Write Tests
- Vitest for hooks and pure logic with non-trivial behavior (filter ↔ URL sync, data transformation for charts), co-located (`useFilters.test.ts` next to `useFilters.ts`)
- Run: `npm test`, `npm run lint`, `npm run build`

### 7. User Review
- Tell the user to start the backend (`uv run uvicorn app.main:app --reload` in `backend/`) and the frontend (`npm run dev` in `frontend/`) and open localhost:5173
- Ask: "Does the UI look right? Any changes needed?"
- Iterate based on feedback

## Context Recovery
If your context was compacted mid-task:
1. Re-read the feature spec you're implementing
2. Re-read `features/INDEX.md` for current status
3. Run `git diff` to see what you've already changed
4. Run `git ls-files frontend/src/components/ | head -30` to see current component state
5. Continue from where you left off - don't restart or duplicate work

## After Completion: Handoff

Check whether the required API endpoints exist and return real data.

If endpoints are missing:
> "Frontend is done, but the endpoints [list] don't exist yet. Next step: Run `/backend` to build them."

Otherwise:
> "Frontend is done! Next step: Run `/qa` to test this feature against its acceptance criteria."

## Checklist
See [checklist.md](checklist.md) for the full implementation checklist.

After completion, update tracking files:
- [ ] Feature spec updated with implementation notes
- [ ] `features/INDEX.md` status updated to "In Progress"

## Git Commit
```
feat(PROJ-X): Implement frontend for [feature name]
```
