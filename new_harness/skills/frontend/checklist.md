# Frontend Implementation Checklist

Before marking frontend as complete:

## shadcn/ui
- [ ] Checked shadcn/ui for EVERY UI component needed
- [ ] No custom duplicates of shadcn components created
- [ ] Missing shadcn components installed via `npx shadcn@latest add` (in `frontend/`)

## Existing Code
- [ ] Checked existing components via `git ls-files frontend/src/components/`
- [ ] Reused existing components and hooks where possible

## Design
- [ ] Design system applied (`docs/design-system.md`) or preferences clarified with user
- [ ] Component architecture from Solution Architect followed

## Data
- [ ] All data fetched via `frontend/src/lib/api.ts` (typed)
- [ ] Filter state in URL query string
- [ ] Remote jobs shown regardless of region filter and labeled
- [ ] No secrets in `VITE_` variables

## Implementation
- [ ] All planned components implemented
- [ ] All components use Tailwind CSS (no inline styles, no CSS modules)
- [ ] Loading states implemented (spinner/skeleton during data fetches)
- [ ] Error states implemented (API unreachable, invalid filter)
- [ ] Empty states implemented ("no data yet — first nightly run pending", "no jobs match your filter")
- [ ] Scraped content rendered as plain text (no `dangerouslySetInnerHTML`)
- [ ] External links use `rel="noopener noreferrer"`

## Quality
- [ ] Desktop 1440px primary; 768px and 375px usable (no broken layout, no horizontal scroll)
- [ ] Accessibility: semantic HTML, ARIA labels, keyboard navigation, chart text/table alternative
- [ ] TypeScript: no errors, no `any`

## Verification (run before marking complete)
- [ ] `npm run build` passes without errors
- [ ] `npm run lint` passes
- [ ] `npm test` passes
- [ ] All acceptance criteria from feature spec addressed in UI
- [ ] `features/INDEX.md` status updated to "In Progress"

## Completion
- [ ] User has reviewed and approved the UI in the browser
- [ ] Code committed to git
