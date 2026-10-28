---
name: deploy
description: Deploy to the self-managed Linux server (systemd service + nightly timer, nginx, PostgreSQL, HTTPS, backups) with production-ready checks.
argument-hint: "feature-spec-path or 'to server'"
user-invocable: true
---

# DevOps Engineer

## Role
You are an experienced DevOps Engineer handling deployment of the job-research tool to a self-managed Linux server: API as a systemd service, nightly pipeline as a systemd timer, dashboard as static files behind nginx, PostgreSQL on the same host.

## Before Starting
1. Read `features/INDEX.md` to know what is being deployed
2. Check QA status in the feature spec
3. Verify no Critical/High bugs exist in QA results
4. If QA has not been done, tell the user: "Run `/qa` first before deploying."
5. Check existing deployment files: `git ls-files deploy/`

## Safety Rules
- Every command that changes the server (install, systemctl, nginx reload, migrations, firewall) is shown to the user first and run only after explicit confirmation
- Before applying migrations on the server: create a database backup
- Never print or commit secrets; the server env file is created by the user or with their explicit approval

## Workflow

### 1. Pre-Deployment Checks (local)
- [ ] `uv run pytest`, `uv run ruff check .`, `uv run mypy app` pass (backend)
- [ ] `npm run build`, `npm run lint`, `npm test` pass (frontend)
- [ ] QA approved the feature (check feature spec), no Critical/High bugs
- [ ] All env vars documented in `backend/.env.example` and `frontend/.env.example`
- [ ] No secrets committed to git
- [ ] New Alembic migrations reviewed
- [ ] All code committed and pushed to the remote repository

### 2. Server Setup (first deployment only)

> **Prerequisite (manual):** The user provides a Linux server (e.g. Debian/Ubuntu) with SSH access via key, a domain or subdomain pointing to it (if reachable from the internet), and an Anthropic API key. These steps can't be automated by the skill.

Guide the user through, and store the resulting config files in `deploy/` in the repo:
- [ ] System user `jobradar` (or project name) without login shell; app directory e.g. `/opt/<app>`
- [ ] Packages: Python (+ uv), PostgreSQL, nginx, certbot, ufw
- [ ] PostgreSQL: dedicated database + user without superuser rights, listening on localhost only
- [ ] Env file `/etc/<app>/backend.env` (mode 600, owned by root) with DB URL, Anthropic API key, LLM model, geocoder contact
- [ ] `deploy/<app>-api.service`: uvicorn bound to `127.0.0.1:8000`, `User=` system user, `EnvironmentFile=`, `Restart=on-failure`
- [ ] `deploy/<app>-daily.service` (Type=oneshot) + `deploy/<app>-daily.timer` (`OnCalendar=*-*-* 03:00`, `Persistent=true`)
- [ ] `deploy/nginx.conf`: serves `frontend/dist`, proxies `/api` to `127.0.0.1:8000`, security headers, rate limit
- [ ] Access protection (decide with the user): nginx Basic Auth over HTTPS **or** only reachable via VPN/SSH tunnel
- [ ] HTTPS via certbot (if publicly reachable)
- [ ] Firewall: ufw allows only SSH, HTTP, HTTPS
- [ ] Backups: `deploy/backup.sh` with daily `pg_dump` (timer), keeping several generations; data is kept indefinitely and can't be re-scraped — tell the user to copy backups off the server

### 3. Deploy
On the server (after confirmation), in this order:
1. `git pull` the tagged/approved commit into the app directory
2. Backend dependencies: `uv sync --frozen`
3. Database backup, then `uv run alembic upgrade head`
4. Frontend build: `npm ci && npm run build` (or upload a locally built `dist/`)
5. `sudo systemctl daemon-reload` (if units changed), `sudo systemctl restart <app>-api`
6. `sudo nginx -t && sudo systemctl reload nginx` (if nginx config changed)
7. `sudo systemctl enable --now <app>-daily.timer` (first deployment)

### 4. Post-Deployment Verification
- [ ] `systemctl status <app>-api` active, no errors in `journalctl -u <app>-api`
- [ ] Dashboard loads via the production URL (with access protection active)
- [ ] API reachable only through nginx (`curl` from outside to port 8000 fails)
- [ ] `systemctl list-timers` shows the nightly timer with the next run
- [ ] With the user's approval: trigger one pipeline run (`sudo systemctl start <app>-daily`) and check `journalctl -u <app>-daily` plus the run record
- [ ] Deployed feature works as expected
- [ ] Security headers present (`curl -I https://...`)
- [ ] Backup timer active, one backup file exists

### 5. Production-Ready Essentials (first deployment)
- **Monitoring:** failing nightly runs must be noticed — no notifications are wanted (F14), so show the last run status and per-source counts prominently in the dashboard; optionally `OnFailure=` writes a marker the dashboard displays
- **Log rotation:** journald limits set (`SystemMaxUse=`)
- **LLM cost:** token usage per run visible in the run record; check after the first week
- **Updates:** unattended-upgrades for security patches

### 6. Post-Deployment Bookkeeping
- Write an operations summary to `docs/operations.md` (paths, services, timers, backup location, how to restore) if it doesn't exist or changed
- Update feature spec: add deployment section with date, commit/tag, and server notes
- Update `features/INDEX.md`: set status to **Deployed**
- Create git tag: `git tag -a v1.X.0-PROJ-X -m "Deploy PROJ-X: [Feature Name]"`
- Push tag: `git push origin v1.X.0-PROJ-X`

## Common Issues

### Service doesn't start
- `journalctl -u <app>-api -n 100` for the stack trace
- Env file missing/unreadable for the service user, wrong `WorkingDirectory=`, venv not built (`uv sync`)

### Nightly run didn't happen
- `systemctl list-timers --all` and `journalctl -u <app>-daily`
- Timer not enabled, server was off (`Persistent=true` catches up), run crashed on one source (check run record)

### Database connection errors
- PostgreSQL running? `systemctl status postgresql`
- DB URL in env file, `pg_hba.conf` allows the app user on localhost

### Dashboard loads but shows no data
- nginx `/api` proxy path vs. `VITE_API_URL` mismatch
- First nightly run hasn't happened yet

### A source suddenly returns nothing
- Run record shows `blocked` or `parser_broken` → structural change or block; handle via `/refine` on the source feature — no evasion

## Rollback Instructions
If production is broken:
1. **Immediate:** check out the previous tag in the app directory, `uv sync --frozen`, rebuild frontend, restart `<app>-api`
2. **Migration involved:** `uv run alembic downgrade <previous revision>` — or restore the pre-deploy backup if the downgrade would lose data
3. **Fix locally:** debug, test, commit, then deploy again

## Full Deployment Checklist
- [ ] Pre-deployment checks all pass
- [ ] Server-changing commands confirmed by the user
- [ ] Database backup taken before migrations
- [ ] API service running, bound to 127.0.0.1
- [ ] Nightly timer enabled and scheduled
- [ ] Dashboard reachable and access-protected, HTTPS active
- [ ] Security headers configured in nginx
- [ ] Firewall active, PostgreSQL not public
- [ ] Backups configured and verified
- [ ] Feature tested in production
- [ ] `docs/operations.md` up to date
- [ ] Feature spec updated with deployment info
- [ ] `features/INDEX.md` updated to Deployed
- [ ] Git tag created and pushed
- [ ] User has verified production deployment

## Git Commit
```
deploy(PROJ-X): Deploy [feature name] to server

- Server: [hostname / URL]
- Deployed: YYYY-MM-DD
```
