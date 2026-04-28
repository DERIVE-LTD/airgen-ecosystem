# Self-hosting UHT Substrate with Neo4j

The hosted instance at
[substrate.universalhex.org](https://substrate.universalhex.org) is
the easiest way to use the substrate. For data-sovereignty reasons
or large-scale workloads, you may prefer to run your own. This guide
walks through a self-hosted setup against Neo4j.

> **Prerequisites:**
>
> - A Derive Ltd licence that grants access to the UHT Substrate source
>   distribution. Contact [info@derive-ltd.co.uk](mailto:info@derive-ltd.co.uk)
>   to arrange access.
> - Linux or macOS host with Docker, Python 3.11+, and `pip`
> - A UHT Factory API key (sign in at
>   [factory.universalhex.org](https://factory.universalhex.org))
> - About 30 minutes

## 1. Get the substrate source

Clone the substrate source using the URL provided with your licence:

```sh
git clone <your-licensed-substrate-repo-url> uht-substrate
cd uht-substrate
```

## 2. Start Neo4j

Neo4j 5+ is required. The repo ships a `docker-compose.yml`:

```sh
docker-compose up -d
```

This starts Neo4j on the default ports (`7474` for browser,
`7687` for bolt) with persistent volumes. Verify the browser at
`http://localhost:7474` and set your password via the first-run
prompt.

For production, add HTTPS, restrict the bolt port to localhost or
private network, and configure the `dbms.security.procedures.allowlist`
appropriately.

## 3. Install the substrate

```sh
python3 -m venv .venv
source .venv/bin/activate
pip install -e .
```

The `-e` (editable) install lets you pull and re-deploy without
reinstalling.

## 4. Configure

Copy the env template and fill in the required values:

```sh
cp .env.example .env
```

The required settings:

| Variable                      | Required | Purpose                                                       |
| ----------------------------- | -------- | ------------------------------------------------------------- |
| `UHT_NEO4J_PASSWORD`          | yes      | Password set in step 2.                                       |
| `UHT_NEO4J_URI`               | no       | Defaults to `bolt://localhost:7687`.                          |
| `UHT_API_BASE_URL`            | no       | Factory API URL. Defaults to `https://factory.universalhex.org/api/v1`. |
| `UHT_API_KEY`                 | yes      | Factory API key (so the substrate can call the classifier).   |
| `UHT_SERVER_PORT`             | no       | HTTP port. Defaults to `8765`.                                |
| `UHT_CORS_ORIGINS`            | no       | Comma-separated allowed origins for browser clients.          |

Optional OAuth settings (only if you want self-service token
provisioning by Google / GitHub login):

| Variable                      | Purpose                                                       |
| ----------------------------- | ------------------------------------------------------------- |
| `UHT_GOOGLE_CLIENT_ID`        | Google OAuth client ID.                                       |
| `UHT_GOOGLE_CLIENT_SECRET`    | Google OAuth client secret.                                   |
| `UHT_GITHUB_CLIENT_ID`        | GitHub OAuth client ID.                                       |
| `UHT_GITHUB_CLIENT_SECRET`    | GitHub OAuth client secret.                                   |
| `UHT_AUTH_SECRET`             | Secret for signing session cookies (required if OAuth on).    |

OAuth callback URLs:

- `https://your-domain/auth/google/callback`
- `https://your-domain/auth/github/callback`

## 5. Choose a run mode

The substrate runs in one of two modes:

```sh
# Stdio MCP — for direct use from Claude Desktop / Claude Code
uht-substrate

# HTTP — REST + MCP + landing page on UHT_SERVER_PORT
uht-substrate --web
```

For most production deployments you want `--web`. Run it under a
process supervisor (systemd, supervisord, Docker, …).

A minimal systemd unit:

```ini
[Unit]
Description=UHT Substrate
After=network.target

[Service]
Type=simple
EnvironmentFile=/etc/uht-substrate.env
WorkingDirectory=/opt/uht-substrate
ExecStart=/opt/uht-substrate/.venv/bin/uht-substrate --web
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

## 6. Reverse proxy and TLS

Front the substrate with a TLS-terminating reverse proxy. A short
Caddy config:

```
substrate.example.com {
    reverse_proxy localhost:8765
}
```

Or nginx:

```nginx
server {
    listen 443 ssl http2;
    server_name substrate.example.com;
    ssl_certificate /etc/letsencrypt/live/substrate.example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/substrate.example.com/privkey.pem;

    location / {
        proxy_pass http://localhost:8765;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Once the proxy is up, your substrate is at
`https://substrate.example.com/api` (REST) and
`https://substrate.example.com/mcp` (MCP, streamable-http).

## 7. Provision a Bearer token

If you have OAuth configured, users sign in via the landing page to
get a Bearer token. Otherwise, generate one manually using the
substrate's token-generation script (path varies; see the README in
your distribution for the exact command at the time of installation).

## 8. Smoke-test

```sh
curl -H "Authorization: Bearer $TOKEN" \
     https://substrate.example.com/api/info

curl -X POST -H "Authorization: Bearer $TOKEN" \
     -H "Content-Type: application/json" \
     -d '{"entity": "hammer"}' \
     https://substrate.example.com/api/classify
```

Both should return JSON. The first hits Neo4j, the second hits the
Factory API too.

## 9. Operations

### Backups

Neo4j: dump on a schedule with `neo4j-admin database dump`. For
container deployments, mount a backup volume and snapshot it.

The substrate itself is stateless; only Neo4j needs backing up.

### Monitoring

The substrate logs to stdout (structured JSON when
`UHT_LOG_FORMAT=json`). Pipe into your existing log aggregator.
Health endpoint: `GET /api/info` returns 200 if the substrate can
reach Neo4j and the Factory API.

### Upgrades

```sh
cd /opt/uht-substrate
git pull
.venv/bin/pip install -e .
systemctl restart uht-substrate
```

The substrate uses migration scripts (in `scripts/`) for
schema-affecting changes. Review the changelog before pulling.

## What's next

- [Classifying entities for a new project](./classifying-entities-for-a-new-project.md)
- [Connecting the substrate MCP server to Claude Desktop](./connecting-the-substrate-mcp-server-to-claude-desktop.md)
