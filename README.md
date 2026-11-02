# Scheduler System

An appointment booking system with working-hour blocks, per-service durations, conflict rules, and automatic slot assignment. Built with Node.js, Express and SQLite. The UI is in Hebrew (RTL).

<!-- Add 2-3 screenshots here, e.g. booking page, admin page -->
<!-- ![Booking page](docs/booking.png) -->

## Features

- **Two booking modes**
  - *Scheduled services*: the user picks a time from the available slots.
  - *Auto-scheduled services*: the system assigns the next free slot (searches up to 30 days ahead).
- **Working-hour blocks** (Asia/Jerusalem): 08:30-11:30, 16:30-18:30, 20:30-22:00. No Fridays or Saturdays.
- **Per-service durations**: 5 or 15 minutes by service type, or computed from the request for variable-length services.
- **Conflict detection**: at most 2 overlapping appointments per time range, plus per-network rules that block two overlapping sessions on the same network.
- **Availability endpoint** that returns only the slots that actually fit.
- **Admin page** to view and cancel appointments.

## Tech stack

| Layer    | Tech                                  |
|----------|---------------------------------------|
| Backend  | Node.js, Express, SQLite (`sqlite3`)  |
| Dates    | Luxon                                 |
| Frontend | Plain HTML / CSS / JavaScript         |
| CI       | GitHub Actions                        |

## Getting started

**Requirements:** Node.js 18+

```bash
git clone https://github.com/noname1134/scheduler-system.git
cd scheduler-system/backend
npm install
npm start
```

The API runs on `http://localhost:3000`. The SQLite database (`appointments.db`) is created automatically on first run.

To use the frontend, serve the `frontend/` folder with any static server (for example `npx serve frontend`) and open `index.html`.

Set a different port with `PORT=4000 npm start`.

## API

| Method | Endpoint        | Description                                              |
|--------|-----------------|----------------------------------------------------------|
| GET    | `/book`         | List all appointments                                    |
| POST   | `/book`         | Create an appointment (with `startTime`) or auto-assign one (without) |
| DELETE | `/book/:id`     | Cancel an appointment                                    |
| GET    | `/availability` | Available slots for a date: `?date=YYYY-MM-DD&serviceType=...&details={...}` |

Example request:

```bash
curl -X POST http://localhost:3000/book \
  -H "Content-Type: application/json" \
  -d '{"serviceType":"hotzla","startTime":"2026-11-03T09:00:00","phone":"0500000000"}'
```

## How scheduling works

1. The duration is computed from the service type and its details.
2. The requested time must fit entirely inside one working block.
3. The system checks existing appointments for overlap and applies the conflict rules.
4. For auto-scheduled services, it scans slots in 5-minute steps, day by day, and takes the first one that passes all checks.

## Project structure

```
backend/
  server.js        API and scheduling logic
  schema.sql       Database schema
frontend/
  *.html           Booking flow and admin pages
  common.js        Shared frontend helpers
  modern.css       Styles
.github/workflows/ CI
```
