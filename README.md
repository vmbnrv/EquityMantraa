# EquityMantraa - Ghost Membership Site

Complete self-hosted Ghost CMS setup for membership/newsletter site with Docker + Traefik.

## 🚀 Quick Start (Existing Server)

```bash
cd /root/ghost-prod
docker-compose -f docker-compose.prod.yml up -d
```

Visit: **https://equitymantraa.com**

## 📦 What's Included

- **Ghost 5.130.5** (Alpine Docker image)
- **Traefik v3.1** (Reverse proxy with automatic HTTPS via Let's Encrypt)
- **SQLite database** (manual backups in `backups/` folder)
- **Customized Source theme** (`source-local`) with:
  - Footer: Centered copyright, T&C link on right
  - T&C page with smaller font and reduced spacing
  - Signup portal with required T&C agreement checkbox
- **Resend SMTP** for transactional emails

## 🔧 Migration to New Provider

### Prerequisites
- New VPS with Docker & Docker Compose installed
- Domain DNS pointed to new server IP
- SSH access to new server

### Step 1: Clone Repository
```bash
git clone git@github.com:vmbnrv/EquityMantraa.git
cd EquityMantraa
```

### Step 2: Create Environment File
```bash
cp .env.template .env
```

Edit `.env` with your credentials:
```env
# SMTP Configuration (Resend)
SMTP_HOST=smtp.resend.com
SMTP_PORT=465
SMTP_USER=resend
SMTP_PASS=your_resend_api_key_here

# Ghost Configuration
GHOST_URL=https://equitymantraa.com
ACME_EMAIL=admin@equitymantraa.com
```

### Step 3: Restore Database
```bash
# Copy latest backup to active database location
cp backups/ghost.db.with-signup-tc-20260104-134327 ghost-content/data/ghost.db
```

**Available backups:**
- `ghost.db.with-signup-tc-20260104-134327` (Latest - includes T&C checkbox)
- `ghost.db.with-tc-menu-20260104-132811` (T&C page + menu)
- `ghost.db.baseline-20260104-130237` (Clean baseline with admin)
- `ghost.db.20260104-124326` (Initial backup)

### Step 4: Deploy
```bash
# Production deployment
docker-compose -f docker-compose.prod.yml up -d

# Check logs
docker-compose -f docker-compose.prod.yml logs -f ghost
docker-compose -f docker-compose.prod.yml logs -f traefik
```

### Step 5: Verify
```bash
# Check HTTPS certificate
curl -I https://equitymantraa.com

# Should return: HTTP/2 200
```

**Admin login:** https://equitymantraa.com/ghost

**Credentials:** equitymantraa@gmail.com (check database backup for password)

## 🏗️ Architecture

```
Internet
   ↓
Traefik (Port 80/443) → Let's Encrypt ACME
   ↓
Ghost (Port 2368) → SQLite Database
   ↓
Resend SMTP → Email delivery
```

**Volumes:**
- `./ghost-content:/var/lib/ghost/content` - Themes, images, database
- `./letsencrypt:/letsencrypt` - SSL certificates (auto-renewed)

## 📝 Customizations Applied

### Theme: Source (source-local)
- **Footer layout:** Logo left, Copyright center, T&C right
- **Code injection removed:** No longer using CSS override for footer

### Terms & Conditions Page
- **URL:** `/terms-and-conditions/`
- **Custom styling:** Smaller font (0.9em), reduced paragraph spacing (0.5em)
- **Page Header Code Injection:**
```css
.gh-content {
  font-size: 0.9em !important;
  color: #555;
}
.gh-content > *,
.gh-content p {
  margin-top: 0.5em !important;
  margin-bottom: 0.5em !important;
  line-height: 1.4 !important;
}
```

### Signup Portal
- **Required T&C agreement:** Checkbox with link to T&C page
- **Configured in:** Ghost Admin → Settings → Access → Portal → "Display notice at signup"
- **HTML:**
```html
I agree to the <a href="/terms-and-conditions/" target="_blank" style="color: #667eea; font-weight: bold; text-decoration: underline;">Terms & Conditions</a>
```

## 🔄 Database Backup Workflow

Ghost database is **NOT tracked in git** (binary conflicts). Manual backups only.

### Create Backup
```bash
# On production server
scp root@45.32.219.61:/root/ghost-prod/ghost-content/data/ghost.db \
    backups/ghost.db.$(date +%Y%m%d-%H%M%S)

# Commit to GitHub
git add backups/
git commit -m "Backup: Description of changes"
git push
```

### Restore Backup
```bash
# From local machine to production
scp backups/ghost.db.with-signup-tc-20260104-134327 \
    root@45.32.219.61:/root/ghost-prod/ghost-content/data/ghost.db

# Restart Ghost
ssh root@45.32.219.61 "cd /root/ghost-prod && docker-compose -f docker-compose.prod.yml restart ghost"
```

**See:** [`docs/DATABASE_BACKUP.md`](docs/DATABASE_BACKUP.md) for detailed workflow.

## 🌐 Current Deployment

**Provider:** Vultr VPS  
**IP:** 45.32.219.61  
**SSH:** `ssh root@45.32.219.61`  
**Domain:** equitymantraa.com  
**Location:** `/root/ghost-prod/`  

**Cost:** ~$6/month VPS + $12/year domain = **$7/month total**

## 📚 Documentation

- [`docs/DATABASE_BACKUP.md`](docs/DATABASE_BACKUP.md) - Database backup workflow
- [`docs/ghost_portable.md`](docs/ghost_portable.md) - Portability guide
- [`MEMBERSHIP_SETUP.md`](MEMBERSHIP_SETUP.md) - Membership configuration
- [`TERMS_SETUP_GUIDE.md`](TERMS_SETUP_GUIDE.md) - T&C page setup
- [`GHOST_CODE_INJECTION_MASTER.html`](GHOST_CODE_INJECTION_MASTER.html) - Code snippets

## 🔐 Security Notes

- **`.env` file:** Contains SMTP password, never committed to git
- **Database:** Manual backups only (`.gitignore` excludes live DB)
- **Secrets:** Use `.env.template` as reference, create `.env` locally
- **SSL:** Automatic renewal via Traefik/Let's Encrypt

## 🛠️ Development

```bash
# Local development stack (no HTTPS)
docker-compose up -d

# Access: http://localhost:2368
```

## 📊 Tech Stack

| Component | Version | Purpose |
|-----------|---------|---------|
| Ghost | 5.130.5 | CMS/Publishing platform |
| Traefik | v3.1 | Reverse proxy & SSL |
| Docker | Latest | Containerization |
| SQLite | 3.x | Database |
| Resend | API | Transactional email |
| Node.js | 18 | Ghost runtime |

## 🆘 Troubleshooting

### Ghost won't start
```bash
# Check logs
docker-compose -f docker-compose.prod.yml logs ghost

# Common issues:
# 1. Database locked: Restart container
# 2. Port conflict: Check if old containers running
# 3. Volume permissions: chown -R node:node ghost-content/
```

### HTTPS not working
```bash
# Check Traefik logs
docker-compose -f docker-compose.prod.yml logs traefik

# Verify DNS
dig equitymantraa.com

# Force cert renewal
docker-compose -f docker-compose.prod.yml restart traefik
```

### Database corruption
```bash
# Restore from backup
cp backups/ghost.db.with-signup-tc-20260104-134327 ghost-content/data/ghost.db
docker-compose -f docker-compose.prod.yml restart ghost
```

## 📞 Contact

**Site:** https://equitymantraa.com  
**Admin:** equitymantraa@gmail.com  
**GitHub:** https://github.com/vmbnrv/EquityMantraa

---

**Last Updated:** January 4, 2026
