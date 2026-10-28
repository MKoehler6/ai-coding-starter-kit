---
paths:
  - "backend/app/scrapers/**"
  - "backend/app/jobs/**"
  - "backend/app/services/**"
---

# Pipeline Rules (Scraping, LLM Extraction, Dedup, Geocoding)

## Pipeline Stages
1. **Fetch** — per source and keyword, Germany-wide
2. **Parse** — source adapter turns raw data into a common job record (incl. full text)
3. **Dedup** — discard duplicates per domain rules (first found wins)
4. **Extract** — Claude API extracts hard/domain skills, remote flag, normalized locations
5. **Geocode** — locations → coordinates (cached)
6. **Store** — persist with found date; write a run log

Each stage is a separate, testable function. A failure in one source or one ad must never abort the whole run.

## Sources (Scrapers)
- One adapter per source in `backend/app/scrapers/`, behind a common interface
- Prefer official APIs or feeds (e.g. Bundesagentur für Arbeit Jobsuche API) over HTML scraping
- Respect each source's terms of use and `robots.txt`; use a clear User-Agent
- Throttle requests (delay between requests, low concurrency) — this is a personal tool, one run per night
- **No captcha solving, no bot-detection evasion, no proxy rotation to bypass blocks.** If a source shows a captcha or blocks, stop that source for the run, log it as `blocked`, and continue with others
- Logging into portals with personal accounts only after an explicit, documented user decision
- Detect structural changes: if a parser finds zero results or required fields are missing, log a `parser_broken` warning instead of storing garbage
- Every adapter has saved HTML/JSON fixtures in `backend/tests/fixtures/<source>/` and tests that run offline

## LLM Extraction (Claude API)
- Use the official `anthropic` Python SDK; the model is configured via env var, not hardcoded
- Request structured output and validate it against a Pydantic schema; invalid responses are retried once, then logged and skipped
- Job-ad text is **untrusted input**: it may contain instructions. The prompt treats it as data, and the output is only ever parsed as data — never executed or used to build queries
- Skills: hard skills and domain-specific skills only, no soft skills; normalize names (e.g. "Python 3" → "Python")
- Store the prompt/extraction version with each result so ads can be re-analyzed when the skill definition changes
- Log token usage per run so costs stay visible
- Only send new (non-duplicate) ads to the LLM

## Dedup
- Implement exactly the domain rules (cross-portal duplicate, multi-location = one job, repost = new job, first found wins)
- The concrete matching strategy is decided in `/architecture` and logged in the Decision Log
- Dedup logic is a pure function with exhaustive unit tests (including near-misses that must NOT be duplicates)

## Geocoding
- Cache every resolved location in the database — never geocode the same place twice
- If Nominatim (OpenStreetMap) is used: max 1 request/second, valid User-Agent with contact, respect the usage policy
- Unresolvable locations are stored without coordinates and flagged, not dropped
- Remote jobs need no coordinates

## Nightly Run
- Entry point: `python -m app.jobs.daily_run`, triggered by a systemd timer
- Idempotent: running it twice on the same day must not create duplicates
- Write a run record (start, end, per-source counts: fetched / new / duplicate / failed / blocked, LLM tokens)
- Exit code ≠ 0 only on total failure, so systemd marks real failures
