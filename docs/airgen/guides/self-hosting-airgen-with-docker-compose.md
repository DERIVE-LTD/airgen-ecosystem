# Self-hosting AIRGen with Docker Compose

The hosted service at [airgen.studio](https://airgen.studio) is the
fastest path to AIRGen. For data-sovereignty, regulatory, or
network-isolation reasons you may prefer to run your own. This guide
walks through a self-hosted production setup with Traefik, Neo4j 5,
PostgreSQL 16, and Redis 7.

> **Prerequisites:**
>
> - A Derive Ltd licence that grants access to the AIRGen source
>   distribution. Contact [info@derive-ltd.co.uk](mailto:info@derive-ltd.co.uk)
>   to arrange access.
> - Linux host (Docker host: Ubuntu 22.04+ or similar) with at least
>   8 GB RAM, 4 vCPUs, and 100 GB disk.
> - Docker and Docker Compose v2.
> - A domain name pointed at the host's public IP.
> - Roughly 60–90 minutes for a clean run.

The AIRGen distribution ships everything you need: a development
compose stack, a production compose stack with Traefik + TLS, env
templates, backup scripts, and a multi-stage Docker build pipeline.

## Architecture

The stack runs five services:

| Service     | Image                          | Role                                          |
| ----------- | ------------------------------ | --------------------------------------------- |
| Traefik     | `traefik:3`                    | Reverse proxy, TLS termination via Let's Encrypt. |
| Backend     | Built from repo (`backend/`)   | Fastify API server.                           |
| Frontend    | Built from repo (`frontend/`)  | React SPA, served via nginx.                  |
| Neo4j 5     | `neo4j:5`                      | Primary data store.                           |
| PostgreSQL 16 | `postgres:16`                | Auth + metadata catalog.                      |
| Redis 7     | `redis:7`                      | Cache, rate limiting, background jobs.        |

## 1. Get the source

Clone the AIRGen source repository using the URL provided with your
licence, then `cd` into the working copy:

```sh
git clone <your-licensed-airgen-repo-url> airgen
cd airgen
```

## 2. Configure environment

```sh
cp env/production.env.example env/production.env
```

Edit `env/production.env`. The mandatory values:

| Variable                | Notes                                                      |
| ----------------------- | ---------------------------------------------------------- |
| `DOMAIN`                | Your AIRGen domain (e.g. `airgen.example.com`).            |
| `ACME_EMAIL`            | Let's Encrypt account email.                               |
| `GRAPH_PASSWORD`        | Strong password for Neo4j.                                 |
| `POSTGRES_PASSWORD`     | Strong password for PostgreSQL.                            |
| `API_JWT_SECRET`        | Long random string (≥ 64 chars). Don't reuse anywhere.     |
| `LLM_API_KEY`           | OpenAI-compatible API key for AI drafting features (optional but recommended). |
| `LLM_MODEL`             | Model name (default `gpt-4o-mini`).                        |
| `CORS_ORIGINS`          | Comma-separated allowed origins.                           |

For SMTP, 2FA, the imagine feature, and backup remotes, see
`.env.example` in the repo root.

## 3. Start the stack

```sh
docker compose --env-file env/production.env \
  -f docker-compose.prod.yml up -d --build
```

The first run builds the multi-stage Docker images (~5–10 minutes
depending on the host). Watch progress with:

```sh
docker compose -f docker-compose.prod.yml logs -f
```

Once Traefik has finished provisioning the TLS certificate, the
frontend is at `https://<DOMAIN>` and the API is at
`https://<DOMAIN>/api`.

## 4. Create the first admin user

The first registered user is auto-promoted to admin. From your host
or another machine:

1. Open `https://<DOMAIN>/register`.
2. Register with the email you intend to use as the admin.
3. Verify the email via the link sent (requires SMTP config).

Alternatively, set `ENABLE_ADMIN_ROUTES=true` in
`env/production.env` and use the admin recovery endpoints to bootstrap.

## 5. Operational feature flags

| Variable                  | Default | Purpose                                                            |
| ------------------------- | ------- | ------------------------------------------------------------------ |
| `ENABLE_ADMIN_ROUTES`     | `false` | Exposes admin user-management and recovery routes. Off in prod by default. |
| `EMAIL_SYSTEM_BCC`        | `info@airgen.studio` | BCC address for system emails. Override with your own. |

Restart the backend to pick up flag changes:

```sh
docker compose -f docker-compose.prod.yml restart backend
```

## 6. Configure backups

AIRGen ships two backup tracks. See
[Configuring the two-track backup model](./configuring-the-two-track-backup-model.md)
for the full setup; the summary:

- **Full-database track** (daily 02:00, weekly Sunday 03:00): Neo4j
  tar.gz + PostgreSQL `pg_dump` + config, encrypted via restic.
- **Per-project track** (daily 04:00, weekly Sunday 05:00): each
  project as a self-contained Cypher script, also via restic.

Both upload to the same restic remote.

## 7. Smoke test

```sh
# Backend health
curl https://<DOMAIN>/api/health

# Database connectivity
docker compose -f docker-compose.prod.yml exec backend \
  node -e 'console.log("OK")'

# Try a CLI call against the live host
AIRGEN_API_URL=https://<DOMAIN>/api airgen tenants list
```

## 8. Maintenance

### Pulling updates

```sh
cd /path/to/airgen
git pull
docker compose --env-file env/production.env \
  -f docker-compose.prod.yml up -d --build
```

The compose stack rebuilds only the changed images. Database
migrations run automatically on backend startup (Neo4j) — review the
release notes before pulling for any major version.

### Restarting a single service

```sh
docker compose -f docker-compose.prod.yml restart backend
docker compose -f docker-compose.prod.yml restart frontend
```

### Logs

```sh
docker compose -f docker-compose.prod.yml logs -f backend
docker compose -f docker-compose.prod.yml logs -f traefik
```

### Resource sizing

Steady-state, modest usage (≤ 50 active users, ≤ 100 active projects):
the defaults are fine. Heavy usage (high QA throughput, many
parallel imports) benefits from a larger Neo4j heap — set
`NEO4J_dbms_memory_heap_max__size` in `env/production.env`.

## 9. Network and security

Default firewall rules to expose: 80 (Traefik), 443 (Traefik). Bind
all other services to the Docker network only — never expose Neo4j,
PostgreSQL, or Redis to the public internet. The compose file
already follows this pattern.

For private deployments, Traefik can sit behind a corporate VPN or
ingress; just point `DOMAIN` to the internal hostname and disable
ACME (`certificatesResolvers.letsencrypt.acme` block in the Traefik
config) in favour of a corporate certificate.

## What's next

- [Configuring the two-track backup model](./configuring-the-two-track-backup-model.md)
- [Connecting AIRGen to Claude via the MCP server](./connecting-airgen-to-claude-via-the-mcp-server.md)
- [Using the AIRGen CLI](./using-the-airgen-cli.md)
