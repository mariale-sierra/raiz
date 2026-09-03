# raiz

Thin Docker Compose wrapper that builds `../backend` and runs it. Almost no code of its own — the actual API lives in the sibling `backend/` repo.

## Requirements

- Docker Desktop (or an equivalent Docker Engine) running locally.
- A `.env` file in this folder with the same keys the backend needs (`JWT_SECRET`, `DB_USERNAME`, `DB_PASSWORD`, `DB_DATABASE`, `CLOUDFLARE_R2_*`, `OPENAI_API_KEY`, ...). Ask a teammate for a working copy — it's gitignored and never committed.

## Option A — against the shared Azure database (unchanged)

```bash
npm run dev:server
# same as: docker compose up --build
```

Uses `docker-compose.yml` as-is: the backend connects to the Azure Postgres instance configured by `DB_HOST` in `.env`. This is the default flow that has always existed here — nothing about it changed.

## Option B — fully local (backend + Postgres, no Azure)

```bash
npm run dev:local
# same as: docker compose -f docker-compose.yml -f docker-compose.local.yml up --build
```

`docker-compose.local.yml` is an **additive override** (`docker-compose.yml` is never edited): it adds a local Postgres container and points the backend at it (`DB_HOST=db`, `DB_SSL=false`) instead of Azure. It reuses `DB_USERNAME`/`DB_PASSWORD`/`DB_DATABASE` already in your `.env` just to seed the local Postgres user/db — no Azure credentials are used or leave your machine.

The local Postgres starts empty, so the backend's migration runner (`npm run db:migrate`, already wired into the container's start command) creates the full schema and seed data from `backend/database/init|migrations|seeds` on first boot. No manual setup needed.

The API is reachable at `http://localhost:3000` on this machine, and at `http://<this machine's LAN IP>:3000` from another device on the same WiFi (e.g. a teammate's phone running Expo — see `frontend/README.md`'s "Running against a local backend" section, which detects that IP automatically).

## Notes

- The local Postgres is published on host port `5434` (not `5432`), specifically to avoid clashing with any other Postgres you might already have running locally for other projects. The backend still reaches it internally as `db:5432` inside the Docker network regardless. Connect a DB client to `localhost:5434` if you need to inspect the local data directly. If `5434` is also taken on your machine, change only that number in `docker-compose.local.yml`.
- Both flows need port `3000` free on the host for the backend itself.
- Switching between Option A and B is just switching which command you run; no file needs to change back and forth.
