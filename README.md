# RMCL — Research & Management Consultants Ltd

Marketing site and internal CMS for **Research & Management Consultants Ltd (RMCL)**: positioning, core service lines, practice-area deep dives, team, insights, contact, and an **admin panel** for managing About and Insights content. Built with **Next.js 16** (App Router), **Tailwind CSS**, **Framer Motion**, **PostgreSQL**, **Prisma 7**, and **shadcn/ui**-based primitives.

![Next.js](https://img.shields.io/badge/Next.js-16-000000?logo=next.js&logoColor=white)
![React](https://img.shields.io/badge/React-18-61DAFB?logo=react&logoColor=black)
![Tailwind](https://img.shields.io/badge/Tailwind-3-38B2AC?logo=tailwind-css&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-336791?logo=postgresql&logoColor=white)
![Prisma](https://img.shields.io/badge/Prisma-7-2D3748?logo=prisma&logoColor=white)

---

## Features

| Area | What's included |
|------|------------------|
| **Homepage** | Hero, animated partner marquee, Services grid, About sectors (CMS-driven), Insights preview (CMS-driven) |
| **Navigation** | Glass pill, sticky, scroll compaction, mobile sheet |
| **Practice areas** | `/services/[slug]` — SSG from `lib/services-data.ts` |
| **Service line pages** | Dedicated routes under `app/services/<name>/page.tsx` |
| **Insights** | `/insights/[slug]` — CMS database driven |
| **About (dynamic)** | `/about/[slug]` — CMS-driven detail pages |
| **Admin CMS** | `/admin/dashboard` — internal panel for managing About cards and Insight posts |
| **Team** | `/team` — responsive grid, modal bios |
| **Contact** | `/contact` — glass layout, form UI |
| **Legal** | `/privacy`, `/terms` |

---

## Tech Stack

| Layer | Choice |
|--------|--------|
| Framework | Next.js 16 (App Router) |
| UI | React 18, Radix/shadcn-style components, Lucide icons |
| Styling | Tailwind CSS 3.x |
| Motion | Framer Motion |
| Database | PostgreSQL + Prisma 7 ORM + `@prisma/adapter-pg` |
| Auth | Cookie-based HMAC sessions (server-side only) |
| Content | TypeScript modules: `lib/*-data.ts` + PostgreSQL CMS |
| E2E | Playwright |

---

## How the Database Connection Works

The app uses **Prisma 7** with the **`@prisma/adapter-pg`** driver adapter. This means:

- **`lib/db.ts`** (line 1–50) creates the Prisma client using a `pg.Pool` connection
- The `DATABASE_URL` environment variable must be a standard `postgresql://` connection string
- The connection is established at runtime — no build-time DB access needed

**Key files for database configuration:**

| File | Purpose |
|------|---------|
| `.env` (root, line 12) | `DATABASE_URL` — your PostgreSQL connection string |
| `lib/db.ts` (lines 1–50) | Creates Prisma client with `@prisma/adapter-pg` |
| `prisma/schema.prisma` (lines 8–10) | Declares `postgresql` as the datasource provider |
| `prisma.config.ts` (lines 12–14) | Prisma CLI datasource URL override |
| `prisma/seed.ts` | Seed script (uses `DATABASE_URL`) |

---

## Getting Started (Local Development)

**Requirements:** Node.js 18+ (20+ recommended), npm, PostgreSQL 14+.

```bash
cd rmcl--main
npm install
```

### 1. Set up environment variables

Edit `.env` in the project root. Change **line 12** to your PostgreSQL connection string:

### 2. Set up the database

```bash
# Generate Prisma client
npx prisma generate

# Push schema to database (creates all tables)
npx prisma db push

# Seed initial data (6 about items + 6 insight posts + initialization flags)
npm run db:seed
```

### 3. Start the dev server

```bash
npm run dev
```

- Public site: [http://localhost:3000](http://localhost:3000)
- Admin panel: [http://localhost:3000/admin/login](http://localhost:3000/admin/login)

---

## Scripts

| Command | Description |
|---------|-------------|
| `npm run dev` | Dev server (`0.0.0.0:3000`) |
| `npm run build` | Production build |
| `npm run start` | Production server |
| `npm run lint` | ESLint |
| `npm run format` | Prettier write |
| `npm run test` | Playwright E2E |
| `npm run db:push` | Push schema to DB (no migration) |
| `npm run db:studio` | Open Prisma Studio (DB GUI) |

---

## Admin CMS Panel

### Access

- **URL**: `/admin/login`
- **Credentials**: Set via `ADMIN_EMAIL` and `ADMIN_PASSWORD` in `.env`
- **Default**: `admin pmail` / `admin password`

### Capabilities

| Feature | Details |
|---------|---------|
| **About Section Manager** | Create, edit, delete, reorder, publish/unpublish cards; upload images; edit slugs |
| **Insights Manager** | Create, edit, delete, publish/unpublish posts; upload images; auto-generate slugs |
| **Image Upload** | Validates type (jpg/png/webp) and size (max 5MB); saves to `public/uploads/` |
| **Instant Publishing** | Changes appear on the live site immediately via `revalidatePath` |
| **Auto Slug Generation** | Titles automatically converted to URL-safe slugs |

---

## Connecting to a Real PostgreSQL Database

### The 2 places you must update

When connecting to any real PostgreSQL database (Hostinger, DigitalOcean, Neon, Supabase, etc.), update **exactly these 2 locations**:

#### File 1: `.env` — line 12
```env
DATABASE_URL=postgresql://USERNAME:PASSWORD@HOST:PORT/DATABASE_NAME?sslmode=require
```

#### File 2: `prisma.config.ts` — line 13 (datasource url)
This file reads from `process.env["DATABASE_URL"]` automatically — no change needed if `.env` is correct.

That's it. The rest of the codebase reads from `process.env.DATABASE_URL` automatically. Run `npm run db:seed` to seed the database.

---

## Deployment: Hostinger VPS

### Step 1 — Purchase and access your VPS

1. Buy a VPS plan at [hostinger.com/vps-hosting](https://www.hostinger.com/vps-hosting) — minimum **2GB RAM**, Ubuntu 22.04 LTS.
2. SSH into your server:
   ```bash
   ssh root@YOUR_VPS_IP
   ```

### Step 2 — Install Node.js 20 and PostgreSQL

```bash
# Update system
apt update && apt upgrade -y

# Install Node.js 20
curl -fsSL https://deb.nodesource.com/setup_20.x | bash -
apt-get install -y nodejs

# Install PostgreSQL
apt-get install -y postgresql postgresql-contrib

# Install Git and Nginx
apt-get install -y git nginx
```

### Step 3 — Create PostgreSQL database

```bash
# Switch to postgres user
sudo -u postgres psql

# Run these SQL commands:
CREATE DATABASE rmcl_cms;
CREATE USER rmcl_user WITH ENCRYPTED PASSWORD 'YourStrongPassword123!';
GRANT ALL PRIVILEGES ON DATABASE rmcl_cms TO rmcl_user;
ALTER DATABASE rmcl_cms OWNER TO rmcl_user;
\q
```

Your connection string will be:
```
postgresql://rmcl_user:YourStrongPassword123!@localhost:5432/rmcl_cms
```

### Step 4 — Clone and configure the project

```bash
# Clone your repository
cd /var/www
git clone https://github.com/YOUR_ORG/rmcl-website.git rmcl
cd rmcl

# Install dependencies
npm install
```

**Edit `.env` — change line 12:**
```bash
nano .env
```
Replace line 12 with:
```env
DATABASE_URL=postgresql://rmcl_user:YourStrongPassword123!@localhost:5432/rmcl_cms
```
Also update line 16 (`ADMIN_PASSWORD`) and line 19 (`SESSION_SECRET`):
```env
ADMIN_PASSWORD=YourSecureAdminPassword
SESSION_SECRET=run-openssl-rand-hex-32-to-generate-this
```
Save: `Ctrl+O`, `Enter`, `Ctrl+X`

### Step 5 — Initialize database and build

```bash
# Generate Prisma client
npx prisma generate

# Create all database tables
npx prisma db push

# Seed initial content (6 About + 6 Insights + initialization flags)
npm run db:seed

# Build the Next.js app
npm run build
```

### Step 6 — Run with PM2

```bash
# Install PM2 globally
npm install -g pm2

# Start the app
pm2 start npm --name "rmcl" -- start

# Save PM2 config and enable auto-start on reboot
pm2 save
pm2 startup systemd
# Run the command PM2 outputs
```

### Step 7 — Configure Nginx

```bash
# Create Nginx config
cat > /etc/nginx/sites-available/rmcl << 'EOF'
server {
    listen 80;
    server_name your-domain.com www.your-domain.com;

    # Allow large image uploads (up to 10MB)
    client_max_body_size 10M;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
}
EOF

# Enable the site
ln -s /etc/nginx/sites-available/rmcl /etc/nginx/sites-enabled/
rm -f /etc/nginx/sites-enabled/default
nginx -t
systemctl restart nginx
```

### Step 8 — Enable HTTPS (free SSL)

```bash
apt-get install -y certbot python3-certbot-nginx
certbot --nginx -d your-domain.com -d www.your-domain.com
```

### Step 9 — Point your domain

In Hostinger hPanel → **DNS Zone** → add an **A record**:
- Name: `@` (and `www`)
- Points to: `YOUR_VPS_IP`
- TTL: 3600

### Step 10 — Verify

- Public site: `https://your-domain.com`
- Admin panel: `https://your-domain.com/admin/login`
- Login with your `ADMIN_EMAIL` / `ADMIN_PASSWORD` from `.env`

---

## Deployment: DigitalOcean

### Option A — DigitalOcean Droplet (VPS)

#### Step 1 — Create a Droplet

1. Log in to [cloud.digitalocean.com](https://cloud.digitalocean.com)
2. Click **Create → Droplets**
3. Choose: **Ubuntu 22.04 LTS**, **Basic plan**, minimum **2GB RAM / 1 vCPU ($12/mo)**
4. Add your SSH key or set a root password
5. Click **Create Droplet**

#### Step 2 — SSH into your Droplet

```bash
ssh root@YOUR_DROPLET_IP
```

#### Step 3 — Install Node.js 20 and PostgreSQL

```bash
# Update system
apt update && apt upgrade -y

# Install Node.js 20
curl -fsSL https://deb.nodesource.com/setup_20.x | bash -
apt-get install -y nodejs

# Install PostgreSQL
apt-get install -y postgresql postgresql-contrib

# Install Git and Nginx
apt-get install -y git nginx
```

#### Step 4 — Create PostgreSQL database

```bash
sudo -u postgres psql

CREATE DATABASE rmcl_cms;
CREATE USER rmcl_user WITH ENCRYPTED PASSWORD 'YourStrongPassword123!';
GRANT ALL PRIVILEGES ON DATABASE rmcl_cms TO rmcl_user;
ALTER DATABASE rmcl_cms OWNER TO rmcl_user;
\q
```

Your connection string:
```
postgresql://rmcl_user:YourStrongPassword123!@localhost:5432/rmcl_cms
```

#### Step 5 — Clone and configure

```bash
cd /var/www
git clone https://github.com/YOUR_ORG/rmcl-website.git rmcl
cd rmcl
npm install
```

**Edit `.env` — change line 12:**
```bash
nano .env
```
```env
DATABASE_URL=postgresql://rmcl_user:YourStrongPassword123!@localhost:5432/rmcl_cms
ADMIN_EMAIL=admin@rmcl
ADMIN_PASSWORD=YourSecureAdminPassword
SESSION_SECRET=generate-with-openssl-rand-hex-32
NODE_ENV=production
```

#### Step 6 — Initialize database and build

```bash
npx prisma generate
npx prisma db push
npm run db:seed
npm run build
```

#### Step 7 — Run with PM2

```bash
npm install -g pm2
pm2 start npm --name "rmcl" -- start
pm2 save
pm2 startup systemd
# Run the command PM2 outputs
```

#### Step 8 — Configure Nginx

```bash
cat > /etc/nginx/sites-available/rmcl << 'EOF'
server {
    listen 80;
    server_name your-domain.com www.your-domain.com;
    client_max_body_size 10M;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
}
EOF

ln -s /etc/nginx/sites-available/rmcl /etc/nginx/sites-enabled/
rm -f /etc/nginx/sites-enabled/default
nginx -t && systemctl restart nginx
```

#### Step 9 — Enable HTTPS

```bash
apt-get install -y certbot python3-certbot-nginx
certbot --nginx -d your-domain.com -d www.your-domain.com
```

#### Step 10 — Point your domain

In your domain registrar's DNS settings, add:
- **A record**: `@` → `YOUR_DROPLET_IP`
- **A record**: `www` → `YOUR_DROPLET_IP`

Or use DigitalOcean's nameservers and manage DNS in the DO control panel.

---

### Option B — DigitalOcean Managed PostgreSQL + App Platform

This is the easiest DigitalOcean setup — no server management needed.

#### Step 1 — Create a Managed PostgreSQL Database

1. In DO control panel → **Databases → Create Database**
2. Choose **PostgreSQL 16**, select your region, choose the **Basic $15/mo** plan
3. Wait for it to provision (~2 minutes)
4. Go to the database → **Connection Details** → copy the **Connection String** (URI format):
   ```
   postgresql://doadmin:GENERATED_PASSWORD@db-postgresql-xxx.db.ondigitalocean.com:25060/defaultdb?sslmode=require
   ```

#### Step 2 — Deploy to App Platform

1. In DO control panel → **Apps → Create App**
2. Connect your **GitHub repository**
3. Select the branch (`main`)
4. DO will detect it as a Node.js app

#### Step 3 — Configure environment variables

In App Platform → **Settings → App-Level Environment Variables**, add:

| Key | Value |
|-----|-------|
| `DATABASE_URL` | `postgresql://doadmin:PASSWORD@your-db-host:25060/defaultdb?sslmode=require` |
| `ADMIN_EMAIL` | `admin@rmcl` |
| `ADMIN_PASSWORD` | `YourSecureAdminPassword` |
| `SESSION_SECRET` | `your-random-64-char-string` |
| `NODE_ENV` | `production` |

#### Step 4 — Set build and run commands

In App Platform → **Settings → Components → Build & Run**:
- **Build Command**: `npm install && npx prisma generate && npm run build`
- **Run Command**: `npm run start`
- **HTTP Port**: `3000`

#### Step 5 — Initialize the database (run once)

On your **local machine**, with the production DATABASE_URL:

**Edit `.env` line 12** temporarily with the production URL, then:
```bash
npx prisma db push
```

Run the seed using the production URL already set in `.env`, then:
```bash
npm run db:seed
```

Restore your local `.env` after seeding.

#### Step 6 — Deploy

Click **Deploy** in App Platform. DO will build and start the app automatically.

---

## Using External Managed PostgreSQL (Neon / Supabase)

If you prefer a free managed PostgreSQL service, the setup is identical — just update the `.env` file:

### Neon (free tier — recommended)

1. Sign up at [neon.tech](https://neon.tech)
2. Create a project → copy the **Connection String**:
   ```
   postgresql://username:password@ep-xxx.us-east-2.aws.neon.tech/neondb?sslmode=require
   ```
3. **Edit `.env` line 12**:
   ```env
   DATABASE_URL=postgresql://username:password@ep-xxx.us-east-2.aws.neon.tech/neondb?sslmode=require
   ```
4. Run:
   ```bash
   npx prisma generate
   npx prisma db push
   npm run db:seed
   ```

### Supabase (free tier)

1. Sign up at [supabase.com](https://supabase.com) → create a project
2. Go to **Settings → Database → Connection String → URI**:
   ```
   postgresql://postgres:YOUR_PASSWORD@db.xxxx.supabase.co:5432/postgres
   ```
3. **Edit `.env` line 12**:
   ```env
   DATABASE_URL=postgresql://postgres:YOUR_PASSWORD@db.xxxx.supabase.co:5432/postgres?sslmode=require
   ```
4. Run:
   ```bash
   npx prisma generate
   npx prisma db push
   npm run db:seed
   ```

---

## Database Setup Checklist (All Platforms)

After deploying to any hosting service, verify each step:

- [ ] PostgreSQL database created and accessible
- [ ] **`.env` line 12** — `DATABASE_URL` set to your connection string
- [ ] `ADMIN_EMAIL` and `ADMIN_PASSWORD` changed from defaults
- [ ] `SESSION_SECRET` set to a random 64-character string
- [ ] `npx prisma generate` run successfully
- [ ] `npx prisma db push` run successfully (creates tables)
- [ ] `npm run db:seed` run successfully (populates 6 About + 6 Insights)
- [ ] `public/uploads/` directory exists and is writable by the Node process
- [ ] Admin login tested at `/admin/login`
- [ ] Admin dashboard shows 6 About cards and 6 Insights posts

---

## Production Security Checklist

- [ ] **Change default credentials** — never use `Dhaka@2026` in production
- [ ] **Use password hash** — set `ADMIN_PASSWORD_HASH` instead of plaintext `ADMIN_PASSWORD`:
  ```bash
  node -e "const c=require('crypto');const s=c.randomBytes(16).toString('hex');console.log(s+':'+c.scryptSync('YourPassword',s,64).toString('hex'))"
  ```
  Then in `.env`:
  ```env
  ADMIN_PASSWORD_HASH=the-output-from-above
  ```
- [ ] **Strong SESSION_SECRET** — generate with `openssl rand -hex 32`
- [ ] **SSL/TLS** — always use `?sslmode=require` in production `DATABASE_URL`
- [ ] **Image uploads** — `public/uploads/` must persist across deploys
- [ ] **Backups** — set up automated PostgreSQL backups on your hosting provider
- [ ] **Firewall** — only expose ports 80, 443 publicly; keep 5432 internal only

---

## Project Structure

```
.env                          ← DATABASE_URL on line 12 — CHANGE THIS
prisma/
  schema.prisma               ← Database models (AboutItem, InsightPost, CmsSetting)
  seed.ts                     ← Seed script (uses DATABASE_URL)
prisma.config.ts              ← Prisma CLI config — reads DATABASE_URL from .env
lib/
  db.ts                       ← Prisma client creation with @prisma/adapter-pg
  cms-data.ts                 ← Public data fetching (published items only)
  auth.ts                     ← Admin authentication
app/
  page.tsx                    ← Homepage (fetches CMS data)
  admin/
    login/page.tsx            ← Admin login page
    dashboard/
      page.tsx                ← Dashboard overview
      about/page.tsx          ← About section manager
      insights/page.tsx       ← Insights manager
  api/admin/
    login/route.ts            ← Login API
    logout/route.ts           ← Logout API
    about/route.ts            ← About CRUD (GET/POST)
    about/[id]/route.ts       ← About CRUD (PUT/DELETE)
    insights/route.ts         ← Insights CRUD (GET/POST)
    insights/[id]/route.ts    ← Insights CRUD (PUT/DELETE)
    upload/route.ts           ← Image upload API
  about/[slug]/page.tsx       ← Public about detail page
  insights/[slug]/page.tsx    ← Public insight detail page
components/
  blocks/insights-section.tsx ← Public insights grid (CMS-driven)
  ui/about-us-section.tsx     ← Public about grid (CMS-driven)
public/
  uploads/                    ← CMS-uploaded images (must be writable)
  assets/                     ← Static site images
```

---

## License / Ownership

Site content and branding belong to **RMCL**. Add a `LICENSE` file if you publish tooling separately.

---

## Maintainer Note

When routes, practice-area slugs, or content modules change, update **README.md** (features summary) and **DOCUMENTATION.md** (tables + project status).

The file that must always be updated when changing the database connection:
1. **`.env` line 12** — `DATABASE_URL`
