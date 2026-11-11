# FeedBox volumes and networking

FeedBox is a minimal feedback collector with PostgreSQL (`db`), Express (`api`), and an Nginx-served frontend (`web`).

## Run

Copy `.env.example` to `.env` and choose a local PostgreSQL password. Update both `POSTGRES_PASSWORD` and the password in `DATABASE_URL` to match (URL-encode special characters in the URL).

```bash
cp .env.example .env
docker compose config -q
docker compose up --build
```

Open http://localhost:3000. The API is also available at http://localhost:5000/api/messages.

PostgreSQL stores its data in the named `postgres_data` volume, so messages survive container recreation and `docker compose down`. To intentionally remove the data, run `docker compose down -v`. Database credentials are used when the volume is first initialized; changing them later requires updating the existing database or recreating the volume.

The database is only connected to the internal backend network. The API joins both backend and frontend networks, and the web service joins the frontend network. The database has no published host port. The browser accesses the published API port directly.

## Development

Use the additional development configuration to mount the API source and frontend page and restart the API on changes:

```bash
docker compose -f docker-compose.yml -f docker-compose.dev.yml config -q
docker compose -f docker-compose.yml -f docker-compose.dev.yml up --build
```

The API's container dependencies are kept in a separate volume so the source mount does not hide them. Refresh the browser after editing the frontend. Do not commit `.env` or `node_modules`.
