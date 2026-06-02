# RMCL Official Platform

A professional organizational website and internal CMS platform developed for **Research and Management Consultants Ltd. (RMCL)**. The platform presents RMCL’s institutional profile, consulting services, practice areas, team capacity, insights, and client communication channels, while also providing an admin panel for managing selected website content.

![RMCL Platform Preview](./screenshots/rmcl-homepage.png)

---

## Live Website

[Visit RMCL Official Website](https://rm-consultants.asia/)

---

## Overview

The **RMCL Official Platform** is a modern web application built to strengthen RMCL’s digital presence as a research, monitoring and evaluation, technical assistance, data analytics, policy advisory, and digital transformation consulting organization.

The platform combines a public-facing institutional website with a lightweight internal CMS. It supports dynamic About and Insights content, structured service pages, team presentation, responsive navigation, and a secure admin dashboard for content management.

The project was designed with emphasis on:

* institutional credibility
* clean service presentation
* mobile-responsive user experience
* CMS-driven content management
* production-ready deployment
* secure server-side authentication
* scalable PostgreSQL-backed content architecture

---

## Website Screenshots

### Homepage

![RMCL Homepage](./screenshots/rmcl-homepage.png)

### Services Section

![RMCL Services](./screenshots/rmcl-services.png)

### About / Institutional Capacity

![RMCL About Section](./screenshots/rmcl-about-practice-areas.png)

### Insights Section

![RMCL Insights](./screenshots/rmcl-insights.png)

### Team Section

![RMCL Team](./screenshots/rmcl-team.png)

### Contact Section

![RMCL Contact](./screenshots/rmcl-contact.png)

### Admin CMS Dashboard

![RMCL Admin Dashboard](./screenshots/rmcl-admin-dashboard.png)

---

## Key Features

| Area             | Description                                                                           |
| ---------------- | ------------------------------------------------------------------------------------- |
| Public Website   | Professional organizational website for RMCL                                          |
| Homepage         | Hero, partner marquee, services, about preview, insights preview, and contact pathway |
| Services         | Structured consulting service lines and practice-area pages                           |
| Dynamic About    | CMS-driven About cards and detail pages                                               |
| Dynamic Insights | CMS-managed insight posts and detail pages                                            |
| Admin Panel      | Internal dashboard for managing About and Insights content                            |
| Team Section     | Responsive team grid with profile details                                             |
| Contact Page     | Professional contact interface for clients and partners                               |
| Authentication   | Cookie-based HMAC server-side session handling                                        |
| Database         | PostgreSQL-backed CMS using Prisma ORM                                                |
| Deployment       | Production-ready setup for VPS, DigitalOcean, and managed PostgreSQL environments     |

---

## Core Website Sections

### Home

Introduces RMCL’s organizational identity, consulting positioning, and sectoral relevance through a professional landing experience.

### Services

Presents RMCL’s major service lines, including research, monitoring and evaluation, technical assistance, training, data analytics, policy advisory, and digital transformation.

### Practice Areas

Dedicated service and practice-area pages provide deeper explanation of RMCL’s consulting focus and institutional capabilities.

### About

CMS-driven institutional cards communicate RMCL’s strengths, working areas, operational approach, and organizational identity.

### Insights

Dynamic knowledge-sharing section for updates, articles, research summaries, and organizational insights.

### Team

Displays RMCL’s team and professional capacity through structured profiles and responsive layouts.

### Contact

Provides a clean and accessible communication pathway for clients, partners, and stakeholders.

---

## Admin CMS

The platform includes a secure internal admin panel for selected content management.

### Admin Route

```txt
/admin/login
```

### CMS Capabilities

| Feature               | Description                                                       |
| --------------------- | ----------------------------------------------------------------- |
| About Section Manager | Create, edit, delete, reorder, publish, and unpublish About cards |
| Insights Manager      | Create, edit, delete, publish, and unpublish insight posts        |
| Image Upload          | Upload validated images for CMS content                           |
| Slug Management       | Auto-generate and edit URL-safe slugs                             |
| Instant Publishing    | Uses revalidation so updated content appears on the public site   |
| Server-Side Auth      | Uses secure cookie-based server-side session handling             |

---

## Technology Stack

| Layer         | Technology                                 |
| ------------- | ------------------------------------------ |
| Framework     | Next.js 16 App Router                      |
| Frontend      | React 18                                   |
| Styling       | Tailwind CSS                               |
| UI Components | shadcn/ui-style primitives, Radix patterns |
| Motion        | Framer Motion                              |
| Icons         | Lucide React                               |
| Database      | PostgreSQL                                 |
| ORM           | Prisma 7                                   |
| DB Adapter    | `@prisma/adapter-pg`                       |
| Auth          | Cookie-based HMAC sessions                 |
| Testing       | Playwright                                 |
| Deployment    | VPS / DigitalOcean / Managed PostgreSQL    |

---

## System Architecture

```txt
User
 │
 ▼
Next.js Public Website
 │
 ├── Static and dynamic public pages
 ├── Service and practice-area pages
 ├── CMS-driven About content
 ├── CMS-driven Insights content
 └── Contact interface

Admin User
 │
 ▼
Admin CMS Panel
 │
 ├── Authentication
 ├── About manager
 ├── Insights manager
 ├── Image upload
 └── Content publishing

Backend Layer
 │
 ├── Next.js API routes
 ├── Server actions / route handlers
 ├── Cookie-based sessions
 └── Prisma ORM

Database Layer
 │
 └── PostgreSQL
```

---

## Project Structure

```txt
app/
  page.tsx
  about/
  admin/
    login/
    dashboard/
  api/
    admin/
  contact/
  insights/
  privacy/
  services/
  team/
  terms/

components/
  blocks/
  layout/
  ui/

lib/
  auth.ts
  cms-data.ts
  db.ts
  services-data.ts

prisma/
  schema.prisma
  seed.ts

public/
  assets/
  uploads/
  screenshots/

styles/
```

---

## Database and CMS Models

The CMS uses PostgreSQL with Prisma. Core models include:

```txt
AboutItem
InsightPost
CmsSetting
```

The application reads published CMS content for the public site and allows admin users to manage selected content through the dashboard.

---

## Environment Variables

Create a `.env` file in the project root.

```env
DATABASE_URL="postgresql://USERNAME:PASSWORD@HOST:PORT/DATABASE_NAME?sslmode=require"

ADMIN_EMAIL="admin@example.com"
ADMIN_PASSWORD="change-this-password"
SESSION_SECRET="generate-a-secure-random-secret"

NODE_ENV="development"
```

For production, never commit `.env` or real credentials to GitHub.

---

## Local Development

### Requirements

* Node.js 18 or later
* npm
* PostgreSQL 14 or later

### Installation

```bash
npm install
```

### Generate Prisma Client

```bash
npx prisma generate
```

### Push Database Schema

```bash
npx prisma db push
```

### Seed Initial CMS Data

```bash
npm run db:seed
```

### Start Development Server

```bash
npm run dev
```

Public site:

```txt
http://localhost:3000
```

Admin login:

```txt
http://localhost:3000/admin/login
```

---

## Available Scripts

| Command             | Description                    |
| ------------------- | ------------------------------ |
| `npm run dev`       | Start development server       |
| `npm run build`     | Create production build        |
| `npm run start`     | Start production server        |
| `npm run lint`      | Run lint checks                |
| `npm run format`    | Format code                    |
| `npm run test`      | Run Playwright tests           |
| `npm run db:push`   | Push Prisma schema to database |
| `npm run db:studio` | Open Prisma Studio             |
| `npm run db:seed`   | Seed initial CMS data          |

---

## Deployment

The platform can be deployed using:

* DigitalOcean Droplet
* Hostinger VPS
* DigitalOcean App Platform
* Managed PostgreSQL providers such as Neon, Supabase, or DigitalOcean Managed Database

### Recommended Production Setup

```txt
Next.js App
 │
PM2 Process Manager
 │
Nginx Reverse Proxy
 │
HTTPS via Certbot
 │
PostgreSQL Database
```

### Production Checklist

* [ ] Set production `DATABASE_URL`
* [ ] Generate Prisma client
* [ ] Push database schema
* [ ] Seed CMS data if needed
* [ ] Set strong `ADMIN_EMAIL`
* [ ] Set strong `ADMIN_PASSWORD` or password hash
* [ ] Set secure `SESSION_SECRET`
* [ ] Run production build
* [ ] Configure PM2 or platform process manager
* [ ] Configure Nginx reverse proxy
* [ ] Enable HTTPS
* [ ] Verify `/admin/login`
* [ ] Verify public About and Insights pages
* [ ] Confirm image uploads work
* [ ] Confirm `public/uploads/` persists in production

---

## Security Considerations

The platform follows basic production security practices:

* server-side authentication
* signed cookie sessions
* environment-variable-based secrets
* no frontend exposure of credentials
* validated image upload handling
* protected admin routes
* HTTPS-ready deployment
* production credential rotation
* PostgreSQL connection through secure connection strings

Recommended production improvements:

* use `ADMIN_PASSWORD_HASH` instead of plaintext password
* enforce strong session secret
* configure database backups
* restrict database port access
* keep uploads persistent across deployments
* monitor application logs
* avoid committing `.env`, upload dumps, or database credentials

---

## Image Uploads

CMS-uploaded images are stored in:

```txt
public/uploads/
```

For production VPS deployment, ensure this directory is writable by the Node.js process and persists after deployment.

---

## My Role

I contributed to the development and improvement of the RMCL platform as a full-stack and frontend-focused implementation project.

Key contribution areas included:

* public website structure
* responsive UI refinement
* service section organization
* CMS content structure
* admin dashboard improvement
* database and Prisma setup support
* deployment-readiness review
* security and environment configuration review
* mobile usability improvement
* production documentation

---

## Outcome

The RMCL Official Platform provides RMCL with a stronger institutional digital presence and a manageable content structure. It supports communication with clients, partners, researchers, and development-sector stakeholders while demonstrating a practical production-grade implementation of a modern organizational website with CMS capabilities.

---

## Screenshots Folder

To display screenshots correctly in this README, add images using the following structure:

```txt
screenshots/
  rmcl-homepage.png
  rmcl-services.png
  rmcl-about.png
  rmcl-insights.png
  rmcl-team.png
  rmcl-contact.png
  rmcl-admin-dashboard.png
```

Keep screenshots compressed for faster GitHub loading.

Recommended size:

```txt
Width: 1200px to 1600px
File size: under 1 MB each
Format: PNG or JPG
```

---

## Links

| Resource     | URL                           |
| ------------ | ----------------------------- |
| Live Website | https://rm-consultants.asia/  |
| Admin Panel  | `/admin/login`                |
| Portfolio    | https://meherabhs.vercel.app/ |

---

## License and Ownership

Website content, brand assets, institutional information, and visual identity belong to **Research and Management Consultants Ltd. (RMCL)**.

Code usage, reuse, and distribution should follow the repository owner’s permission and licensing policy.

---

## Maintainer Note

When changing routes, database models, CMS fields, practice-area slugs, or deployment configuration, update this README and any related technical documentation.

Always verify:

```bash
npm run lint
npm run build
```

before production deployment.
