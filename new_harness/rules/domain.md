# Domain Rules — Job-Research Tool

Settled product decisions from `Job-Recherche Tool – Brainstorming Session.md` (Question Storming, F1–F14).
Every skill and agent must respect them. Changing one requires `/refine` and explicit user approval, logged in the Decision Log of the affected spec.

## Usage
- Personal tool for a single user — no user accounts, no multi-tenancy, no commercial features
- No notifications (no email, push, chat) — the dashboard is the only output (F14)

## Search
- Search runs **Germany-wide per keyword** (e.g. "Informatiker", "Python") (F8)
- The agent runs **once per night** via systemd timer (or cron) (F11)
- Multiple sources (e.g. Bundesagentur für Arbeit, StepStone, Indeed, LinkedIn, Glassdoor, Klimajobs.de, greenjobs.de, nachhaltigejobs.de, jobverde.de) — the final list is decided per feature spec (F1)

## Stored Data per Job
- Company, title, link, required skills, location(s) with coordinates, remote flag, source, keyword(s), **found date**, **full text** (F12)
- Only the found date is stored — no "last seen", no "expired" tracking (F5)
- Data is kept **indefinitely** — no automatic deletion (F13)

## Skills (F2)
- Only **hard skills**: programming languages, IT tools, frameworks
- Plus **domain-specific skills**: e.g. GIS, biology, geography
- **No soft skills** (e.g. teamwork, communication)
- The skill definition is refined iteratively — therefore the full text is stored so ads can be re-analyzed when the definition changes

## Duplicates (F3, F4)
- Same ad on different portals → **duplicate**
- One position with several locations → **one** job
- Reposted ad (re-published later) → **not** a duplicate, counts as a new job (accepted trend bias)
- Recruiter/agency ads are not specially matched against the original employer ad
- Handling: **the first found entry wins**, later matches are discarded

## Region & Remote (F6, F7, F8)
- Region = **city + radius in km** (e.g. "Leipzig, 50 km")
- Region is **only a display filter** in the dashboard, not a search parameter
- Every job location is geocoded to coordinates so the radius can be applied afterwards
- **Remote jobs are always included**, regardless of the region filter

## Trend (F5)
- Trend = **number of new jobs per period** (by found date) per keyword

## Dashboard (F9)
- Trend chart (new jobs per period per keyword)
- Job list (filterable and searchable)
- Skill ranking (most frequent skills and their development over time)
- Map (uses the geocoded coordinates)

## LLM (F10)
- Cloud API (Claude) — volume is low, cost stays manageable but must be logged

## Still Open (decide in `/architecture`, never silently assume)
- How the tool reacts to structural changes at sources
- How captchas and account requirements are handled (constraint: no captcha solving or evasion — see pipeline rules)
- How a cross-portal duplicate is recognized concretely (normalized company + title vs. LLM similarity check)
- Which geocoding service is used (e.g. Nominatim/OpenStreetMap)
- How "remote" is detected reliably (LLM extraction)
