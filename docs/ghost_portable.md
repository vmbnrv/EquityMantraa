# Ghost Portability & Versioning

This repo is set up so your Ghost content and custom theme are bind-mounted and can be tracked in git. Use this as a quick guide for moving between hosts (local ↔ cloud) and keeping changes versioned.

## What is versioned vs. ignored
- Versioned: everything under `ghost-content/` (posts, images, SQLite DB, theme sources) and `ghost-content/themes/casper-local/` which is mounted as the `casper-custom` theme in the container.
- Ignored: runtime TLS state (`letsencrypt/`), Ghost logs, and tool caches (see `.gitignore`).

## Local development
1) `docker compose up -d` (uses `docker-compose.yml`, Mailpit is included for SMTP).  
2) Theme: in Ghost Admin → Settings → Design → Activate the `casper-custom` theme.  
3) Content lives on disk at `ghost-content/` and is safe to edit/commit.  
4) Code injection: paste from `GHOST_CODE_INJECTION_MASTER.html` if you need to re-apply.

## Production (Traefik + Ghost)
1) Set real values in `docker-compose.prod.yml` (ACME email, SMTP host/user/pass, domain labels).  
2) `docker compose -f docker-compose.prod.yml up -d`  
3) Activate the `casper-custom` theme in Ghost Admin once the container is up.  
4) TLS files land in `letsencrypt/` (ignored from git) and Ghost data stays in `ghost-content/`.

## Migrating to another host
1) On the new host: clone this repo.  
2) Bring up the stack (`docker compose ... up -d` for dev or prod).  
3) After Ghost starts, activate `casper-custom` in Admin.  
4) Re-apply code injection from `GHOST_CODE_INJECTION_MASTER.html` if needed.  
5) Verify Mail/Site URL settings match your environment.

## Tips
- Database is the SQLite file at `ghost-content/data/ghost.db`. Commit it if you want full history; otherwise add it to `.gitignore` and use export/import from Ghost Admin.  
- Theme development: edit under `ghost-content/themes/casper-local/`; the container serves it as `casper-custom`.  
- Backups: a simple `git push` captures content + theme; add an external backup for images if the repo size grows.
