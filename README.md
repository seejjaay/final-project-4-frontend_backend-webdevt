# OrgaVents

> Campus event management — React + Vite frontend with an Express + MongoDB API. Runs fully in **Mock Mode** (no server required) or against the **Live Backend** with a single `.env` change.

## Quick Start (Mock Mode — No Server Needed)

```bash
npm install
npm run dev
# → http://localhost:5173
```

Use the **⚡ Instant Demo Login** buttons on the login page (shown only in mock mode under `npm run dev`):

| Role | Email | Password |
|------|-------|----------|
| Admin | `admin@orgavents.edu` | `Password123!` |
| Organizer | `organizer@campus.edu` | `Password123!` |
| Member | `member@campus.edu` | `Password123!` |

> **Reset Demo Data**: Click the "Reset Demo Data" button (bottom-right corner) to wipe `localStorage` back to seed state.

## Switch to Live Backend

Needs Node 20+ and a MongoDB (local `mongod`, Docker, or Atlas).

```bash
npm install
cp .env.example .env        # then edit .env (see below)
npm run seed                # admin account only
npm run seed:demo           # optional: demo users/events/registrations (dev only, idempotent)
npm run migrate:venues      # one-off: link pre-venue events to Venue docs (append `-- --dry` to preview)
npm run server:dev          # API on http://localhost:5000
npm run dev                 # UI on http://localhost:5173 (second terminal)
```

In `.env`: set `VITE_USE_MOCK=false`, point `MONGO_URI` at your database, set `ADMIN_PASSWORD` (required by `npm run seed`), and generate a `JWT_SECRET`:

```bash
node -e "console.log(require('crypto').randomBytes(48).toString('hex'))"
```

The admin (`admin@orgavents.edu`) gets `ADMIN_PASSWORD`. Demo users from `seed:demo` all use `Password123!` — the admin too, if you skip `npm run seed`. `seed:demo` refuses to run when `NODE_ENV=production`. Health check: `GET /api`.

## Project Structure

```
src/
├── pages/           # All UI screens (LoginPage, EventCalendarPage, AdminVenuesPage, etc.)
├── components/      # AppShell, Navbar, calendar/, events/, ui/ (Toast, ConfirmModal, Avatar…)
├── context/         # AuthContext (session, login/logout/register), ThemeContext (dark mode)
├── hooks/           # useDialog, useGoBack
├── utils/           # Date, image, search and validation helpers
└── services/
    ├── api.js           # Dual-mode API client (Mock ↔ Live)
    └── mockBackend.js   # Full localStorage mock implementation
server/              # Express + Mongoose API (config, middleware, models, controllers, routes, scripts)
```

## API

All routes live under `/api` — `users` (auth + profile), `events`, `registrations`, `reports`, `admin` and `venues`. See `server/routes/` for endpoints and roles, and `server/models/` for schemas. Errors come back as JSON with a `message` field.
