# NYCAD Analytics Dashboard

- Data source: posgres SQL
- Backend: Node.js + Express + Postgres, daily sync job
- Frontend: React + Tailwind CSS
- Orchestration: docker-compose
- CI/CD: GitHub Action -> Build & Push Docker images to AWS ECR

## Project Structure

```
.github/workflows/
  backendbuild.yaml
  frontendbuild.yaml
  ci.yaml

/backend
  src/
    server.js
    setup/
      db.js
      init.sql
      routes/
        drivers.js
        stats.js
      services/
        fetchAndStore.js
      utils/
        validation.js
  package.json
  Dockerfile
  .env.example

/frontend
  src/
    App.jsx
    main.jsx
    styles.css
    pages/
      Dashboard.jsx
      Search.jsx
    components/
      BoroughChart.jsx
    lib/
      api.js
  package.json
  Dockerfile
  tailwind.config.js
  postcss.config.js
  vite.config.js

docker-compose.yml
README.md
.gitignore
```

## Backend Setup (Local)

1. Create `backend/.env` from `.env.example` and adjust if needed.
2. Ensure Postgres is running and accessible with the provided credentials.
3. Install deps and start:

```bash
cd backend
npm install
npm run seed # optional: one-time sync
npm run dev  # starts on http://localhost:4000
```

### API Endpoints

- `GET /drivers?borough=Queens&search=smith&page=1&limit=25` — list drivers
- `GET /drivers/:license` — single driver by license
- `GET /stats` — totals and borough breakdown

## Frontend Setup (Local)

```bash
cd frontend
npm install
# Set VITE_API_BASE_URL in .env if backend not on http://localhost:4000
npm run dev  # http://localhost:5173
```

## Run with docker-compose

```bash
docker-compose up --build
```

- Frontend: http://localhost:5173
- Backend: http://localhost:4000
- Postgres: localhost:5432 (user `postgres`, password `postgres`, db `fhv`)

## CI/CD Workflows (GitHub Actions)

This project uses GitHub Actions to build and push Docker iimages to AWS ECR.

- `backendbuild.yaml` -> Builds backend service & pushes Docker image
                         `nycad_backend:<RUN_ID>`
- `frontendbuild.yaml` -> Builds frontend service & pushes Docker image
                          `nycad_frontend:<RUN_ID>`
- `ci.yaml` -> Manual trigger `workflow_dispatch` to run both frontend &       backend builds together

## Notes

- The backend schedules a daily sync (default 3 AM UTC) using `CRON_SCHEDULE` env var.
- You can force a one-time sync by running `npm run seed` in `backend/`.
- Secrets/DB credentials should be managed via environment variables.
