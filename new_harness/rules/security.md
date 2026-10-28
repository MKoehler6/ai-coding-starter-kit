---
paths:
  - "backend/**"
  - "frontend/src/lib/**"
  - "deploy/**"
  - "**/.env*"
---

# Security Rules

## Secrets Management
- NEVER commit secrets, API keys, or credentials to git (Anthropic API key, PostgreSQL password, portal credentials)
- Local development: `backend/.env` and `frontend/.env.local` (both in .gitignore)
- Server: secrets in a root-readable environment file referenced by the systemd unit (`EnvironmentFile=`), never in the unit file itself
- `VITE_` variables end up in the browser bundle — only non-secret values
- Document all required env vars in `backend/.env.example` and `frontend/.env.example` with dummy values

## Untrusted Input
- Scraped HTML, job-ad text, company names, and URLs are untrusted
  - Frontend renders them as text only (stored XSS protection)
  - Only `http(s)` job links are stored and rendered
- Job-ad text sent to the Claude API may contain prompt injection — LLM output is validated against a schema and treated as data only
- Validate ALL API input with Pydantic on the server side
- Use SQLAlchemy bound parameters only (no string-built SQL)

## Access Protection (single-user tool on a public server)
- No user accounts in the app — protect access at the edge: nginx Basic Auth over HTTPS, or reachable only via VPN/SSH tunnel (decided in `/deploy`)
- The FastAPI process binds to `127.0.0.1` only; nginx is the only public entry point
- PostgreSQL listens on localhost only; the app uses a dedicated DB user without superuser rights
- Rate-limit public endpoints in nginx

## Server Hardening
- HTTPS via Let's Encrypt (certbot); HTTP redirects to HTTPS
- Firewall (ufw): only SSH, HTTP, HTTPS open
- Services run as an unprivileged system user, not root
- Regular `pg_dump` backups — data is kept indefinitely and cannot be re-scraped

## Security Headers (set in nginx)
- X-Frame-Options: DENY
- X-Content-Type-Options: nosniff
- Referrer-Policy: strict-origin-when-cross-origin
- Strict-Transport-Security with includeSubDomains
- Content-Security-Policy restricting scripts to self (allow map tile host for images)

## Code Review Triggers
- Any change to nginx access protection requires explicit user approval
- Any change to how sources are accessed (login, new source, request rate) requires explicit user approval
- Any new environment variable must be documented in the matching `.env.example`
