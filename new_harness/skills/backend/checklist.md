# Backend Implementation Checklist

## Core Checklist
- [ ] Checked existing models, migrations, routes, scrapers, services via git before creating new ones
- [ ] Schema changes done via Alembic migration (reviewed by hand, upgrade + downgrade tested)
- [ ] Constraints enforce invariants (unique, not null, foreign keys)
- [ ] Indexes created on filter/sort/aggregation columns
- [ ] All planned endpoints implemented in `backend/app/api/`
- [ ] Pydantic validation on all inputs, with limits
- [ ] All list endpoints paginated
- [ ] Meaningful error messages with correct HTTP status codes
- [ ] No hardcoded secrets; new env vars documented in `backend/.env.example`
- [ ] Frontend API client in sync (if UI exists)
- [ ] User has reviewed and approved

## Domain Rules (see `.claude/rules/domain.md`)
- [ ] Cross-portal duplicate discarded, first found wins
- [ ] Multi-location ad stored as one job
- [ ] Repost stored as a new job
- [ ] Only the found date is stored (no last-seen)
- [ ] Full text stored
- [ ] Only hard and domain-specific skills, normalized
- [ ] Remote jobs always included in region-filtered results

## Pipeline Checklist (if applicable)
- [ ] Adapter throttled, clear User-Agent, terms of use respected
- [ ] No captcha solving / bot-detection evasion; blocked source logged and skipped
- [ ] Structural change detected and logged (`parser_broken`)
- [ ] One failing source or ad doesn't abort the run
- [ ] LLM output validated against Pydantic schema; extraction version stored; token usage logged
- [ ] Only new (non-duplicate) ads sent to the LLM
- [ ] Geocoding cached; rate limit respected; unresolvable locations flagged, not dropped
- [ ] Nightly run idempotent and writes a run record
- [ ] Fixtures saved for every adapter

## Verification (run before marking complete)
- [ ] `uv run pytest` passes (no network access in tests)
- [ ] `uv run ruff check .` and `uv run mypy app` pass
- [ ] All acceptance criteria from feature spec addressed
- [ ] Endpoints return correct status codes (checked with curl or `/docs`)
- [ ] Manual pipeline run with small limit done (with user approval) and run record reviewed
- [ ] `features/INDEX.md` status updated to "In Progress"
- [ ] Code committed to git

## Performance Checklist
- [ ] No N+1 queries (joins / eager loading)
- [ ] Trend and skill aggregations computed in SQL
- [ ] Queries stay fast with growing data (indefinite retention) — checked with `EXPLAIN` for main queries
- [ ] Nightly run duration acceptable for all keywords and sources
