---
paths:
  - "backend/**"
---

# Backend Development Rules (Python / FastAPI / PostgreSQL)

## Database (PostgreSQL + SQLAlchemy + Alembic)
- Every schema change is an Alembic migration (`uv run alembic revision --autogenerate -m "..."`), reviewed before applying
- Never change the database schema by hand, never edit an already applied migration
- Add indexes on columns used in WHERE, ORDER BY, JOIN, and GROUP BY (found date, keyword, source, company)
- Use foreign keys with explicit ON DELETE behavior
- Use `timestamptz` for all timestamps, store in UTC
- Unique constraints enforce dedup invariants at the database level, not only in Python
- Data is kept indefinitely — design for growth (pagination, indexes, no full-table scans in the API)
- No Row Level Security / per-user policies needed (single-user tool)

## API (FastAPI)
- The API is read-mostly: it serves the dashboard (jobs, trends, skills, map data, keyword management)
- Validate all inputs with Pydantic models (query params, path params, bodies), with sensible limits (max page size, radius bounds)
- Return meaningful error messages with appropriate HTTP status codes
- Paginate all list endpoints (default + maximum page size)
- Aggregations (trend, skill ranking) run in SQL, not by loading all rows into Python
- Expose OpenAPI types; the frontend client follows them

## Code Standards
- Type hints everywhere; `mypy` and `ruff` must pass
- Business logic lives in `services/`, not in routers or scrapers
- Configuration via environment variables loaded with pydantic-settings; document every variable in `backend/.env.example`
- Use structured logging (no `print`) — the nightly run must be debuggable from `journalctl`

## Query Patterns
- Avoid N+1 queries — use joins / `selectinload`
- Use bound parameters only — never build SQL with f-strings or string concatenation
- Handle database errors explicitly; a failed source must not roll back data of other sources

## Tests (pytest)
- Unit tests next to the logic they cover in `backend/tests/` (mirroring the `app/` structure)
- Integration tests against a real PostgreSQL test database (not SQLite — behavior differs)
- No network in tests: saved HTML fixtures for scrapers, mocked Claude API and geocoder
