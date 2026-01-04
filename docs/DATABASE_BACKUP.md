# Database Backup & Sync Workflow

Since `ghost.db` is a binary file, tracking it in git causes sync conflicts. Instead, **manually back it up when you make important changes**.

## Quick Backup (After making changes in Ghost)

```bash
# From your local machine
scp root@45.32.219.61:/root/ghost-prod/ghost-content/data/ghost.db ./ghost-content/data/ghost.db

# Save to a timestamped backup
cp ./ghost-content/data/ghost.db ./backups/ghost.db.$(date +%Y%m%d-%H%M%S)
```

## Restore to Server (If DB gets corrupted)

```bash
# From your local machine
scp ./ghost-content/data/ghost.db root@45.32.219.61:/root/ghost-prod/ghost-content/data/ghost.db

# SSH in and restart
ssh root@45.32.219.61 'cd /root/ghost-prod && docker-compose -f docker-compose.prod.yml restart ghost'
```

## Deploy Fresh with Existing DB

1. Clone repo: `git clone https://github.com/vmbnrv/EquityMantraa.git`
2. Copy backed-up database: `cp ./backups/ghost.db.latest ./ghost-content/data/ghost.db`
3. Create `.env` with secrets
4. `docker-compose -f docker-compose.prod.yml up -d`
5. Ghost starts with your admin credentials intact

## Best Practice

- **After significant changes** (new posts, settings, theme tweaks): run the backup command above
- **Before major deploys**: ensure you have a recent backup in `./backups/`
- The production server's database is isolated (not in git) and will persist across restarts unless explicitly deleted

This approach avoids git conflicts while keeping your data safe.
