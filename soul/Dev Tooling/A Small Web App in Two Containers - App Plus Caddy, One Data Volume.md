---
tags: [docker, compose, caddy, deployment, sqlite, tooling]
source: [claude-conversation, 2026-10-01, cflx project]
---
# A Small Web App in Two Containers: App Plus Caddy, One Data Volume

The smallest containerized shape for a small site with a database file: an `app` container (gunicorn) and a `web` container (Caddy) that serves the static files, proxies the dynamic paths, and handles HTTPS. The server then needs only Docker, and `docker compose up` on a fresh machine is the whole setup, which is the cold-clone test from [[Containerization Checklist & Task-Execution Privilege Trade-offs]].

## Mechanics
- **Caddy replaces nginx + certbot.** A domain as the site address gets a certificate and renewal automatically (DNS must point at the host, ports 80/443 open). Make the address an env var with a plain-HTTP default, `{$SITE_ADDRESS::80}`, so the same file works locally and in production.
- **Static files are baked into the `web` image** (`COPY site /srv`). A site edit therefore needs a rebuild; the data is what you change at runtime. Write this down per path: [[Baked, Mounted, or Ephemeral]].
- **Proxy to the service name** (`reverse_proxy app:8000`), never `localhost`: [[An Unset Inter-Service URL Defaults to Localhost, Which Inside a Container Means Itself]].
- **Named-volume ownership: create and chown the mount directory in the image.** `RUN mkdir /data && chown appuser /data` before `USER appuser`. A fresh *named* volume copies the image directory's ownership on first mount, so the non-root app can write immediately. The root-ownership failure in [[Docker Named Volumes (Ownership & Mount-Point Gotchas)]] appears when the path doesn't exist in the image with the right owner.
- **Cold-clone safe config:** read secrets through compose interpolation with safe defaults (`${ADMIN_PASSWORD:-}`, where empty disables the admin page) instead of a required `env_file`, so a missing `.env` doesn't stop `compose up`. Commit `.env.example`, never `.env`.
- **Healthcheck + `depends_on: condition: service_healthy`** makes the proxy wait for the app.
- **Back up from inside the container** with Python's `sqlite3` backup API (the slim image has no `sqlite3` CLI), as in [[Dockerized Postgres - pg_dump Runs Inside the Container, Never the Host]].

## When to use / gotchas
- Deploy = rsync the repo (excluding `.env`, `data`, `.git`) + `docker compose up -d --build`. The data volume is untouched by rebuilds, so orders survive a redeploy.
- `docker compose down -v` deletes the volumes, and with them the database. Never put `-v` in a production script.
- A host port can already be taken (macOS AirPlay Receiver squats on 5000); make the published port an env var.

## Related
[[SQLite Behind a Small Web App - WAL, Server-Side Prices, .backup]] · [[Static Site Behind nginx - Serve Files, Skip the App Server]] · [[Baked, Mounted, or Ephemeral]] · [[Docker Named Volumes (Ownership & Mount-Point Gotchas)]] · [[Containerization Checklist & Task-Execution Privilege Trade-offs]]
