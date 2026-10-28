# PROJ-X: Feature Name

## Status: Planned
**Created:** YYYY-MM-DD
**Last Updated:** YYYY-MM-DD
**Type:** Pipeline | Dashboard | Infrastructure

## Dependencies
- Requires: PROJ-1 (Project Skeleton & Database Setup)

## User Stories
- As the user, I want to [action] so that [goal]
- As the nightly agent, I need to [action] so that [goal]

## Out of Scope
<!-- What this feature explicitly does NOT cover. Critical for developer handoffs. -->
- _Example: Map view of the results (separate feature — PROJ-X)_
- _Example: Login-protected sources (deferred to P2)_

## Acceptance Criteria

**Format:** Angenommen [Vorbedingung] / Wenn [Aktion] / Dann [Ergebnis]

- [ ] Angenommen [Vorbedingung], wenn [Aktion], dann [Ergebnis]
- [ ] Angenommen [Vorbedingung], wenn [Aktion], dann [Ergebnis]

## Edge Cases
- What happens when a source is unreachable / blocked / changed its structure?
- What happens when the same ad appears on several portals?
- What does the dashboard show before the first nightly run?

## Technical Requirements (optional)
- Nightly run: completes within X minutes for all keywords and sources
- API: < 300ms response time for list and trend queries
- LLM cost: max X € per month
- Browsers: Chrome, Firefox (desktop-first)

## Open Questions
<!-- Unresolved questions from the spec interview. Close them in /refine when answered. -->
- [ ] Question 1

## Decision Log
<!-- Record of conscious decisions made and why. Added to by /write-spec and /architecture. -->

### Product Decisions
<!-- Added by /write-spec -->
| Decision | Rationale | Date |
|----------|-----------|------|
| _Example: Repost counts as a new job_ | _Settled in brainstorming (F3); trend bias accepted_ | YYYY-MM-DD |

### Technical Decisions
<!-- Added by /architecture -->
| Decision | Rationale | Date |
|----------|-----------|------|
| _Example: Nominatim for geocoding with DB cache_ | _Free, OSM-based; cache keeps us within 1 req/s_ | YYYY-MM-DD |

---
<!-- Sections below are added by subsequent skills -->

## Tech Design (Solution Architect)
_To be added by /architecture_

## Implementation Notes
_To be added by /backend and /frontend_

## QA Test Results
_To be added by /qa_

## Deployment
_To be added by /deploy_
