# Using the Reify HTTP API

Reify's v1 HTTP API exposes everything the web UI shows — projects,
SysML source, views, diagrams, requirements, trace links, raw
substrate facts, workspace state, dry-run, and audit history — over
a Bearer-authenticated REST interface. It is read-only: writes stay
behind the UI's cookie auth.

This guide walks through authenticating, the conventions, and the
endpoint groups, with copy-paste examples.

> **Prerequisites:** access to a running Reify instance and a Bearer
> token for a user with read access to the projects you want to
> explore.

## Authentication

Two methods, checked in this order per request:

### Bearer token (programmatic)

```
Authorization: Bearer rfy_<base64url>
```

Tokens are sha256-hashed at rest; the plaintext is shown once on
creation and cannot be recovered. All v1 tokens carry the `read`
scope.

### Cookie session (browser)

The same session cookie the Reify UI uses. Useful when poking at v1
from a logged-in browser tab.

Permissions are enforced per project; tokens see only the projects
their user can access.

Failure modes:

- **`401 Unauthorized`** — no credentials, invalid token, or expired
  session.
- **`403 Forbidden`** — authenticated but no access to the requested
  project.

## Generating a token

On the Reify server:

```sh
cd /path/to/reify/app
node --import tsx/esm scripts/generate-api-token.ts \
  github:12345 "Claude Desktop"
```

The plaintext prints once. Store it somewhere private.

## Conventions

The API has a few conventions that reduce surprise:

- **List responses wrap in `{ "items": [...] }`** — never bare
  arrays. Pagination can be added additively later without
  breaking existing consumers.
- **Timestamps are ISO 8601** strings.
- **Fact shape is `{ subject, predicate, object }`** with
  pipe-delimited objects where needed.
- **Errors are `{ "error": "...", "code?": "..." }`**.
- **Caching: every endpoint sets `Cache-Control: no-store`**.
- **Unknown query params are ignored** — you can pass extras safely.

## The endpoint surface

### Projects

```sh
# All projects you can see
curl -H "Authorization: Bearer rfy_..." \
     https://reify.airgen.studio/api/v1/projects

# Detail
curl -H "Authorization: Bearer rfy_..." \
     https://reify.airgen.studio/api/v1/projects/<slug>

# Stats — counts by predicate, document, link type, viewpoint
curl -H "Authorization: Bearer rfy_..." \
     https://reify.airgen.studio/api/v1/projects/<slug>/stats
```

### SysML source

Each project has two SysML sources — `canonical` (substrate +
AIRGen reconstruction) and `workspace` (the current editor state).
Default is `canonical`.

```sh
# Full SysML
curl -H "Authorization: Bearer rfy_..." \
     "https://reify.airgen.studio/api/v1/projects/<slug>/sysml?source=canonical"

# Section index
curl -H "Authorization: Bearer rfy_..." \
     "https://reify.airgen.studio/api/v1/projects/<slug>/sysml/sections"

# Single section body (e.g. parts, hazards, views)
curl -H "Authorization: Bearer rfy_..." \
     "https://reify.airgen.studio/api/v1/projects/<slug>/sysml/sections/parts"
```

### Stateless SysML parser

Useful for tooling — parse arbitrary SysML without persisting
anything:

```sh
curl -X POST -H "Authorization: Bearer rfy_..." \
     -H "Content-Type: application/json" \
     -d '{"sysml": "package X { part def P { } }"}' \
     https://reify.airgen.studio/api/v1/sysml/parse
```

Returns `{ ast, facts, requirements, traceLinks, errors }`.

### Views

```sh
# All views
curl -H "Authorization: Bearer rfy_..." \
     "https://reify.airgen.studio/api/v1/projects/<slug>/views"

# Filter by viewpoint (bdd, ibd, req, saf, ...)
curl -H "Authorization: Bearer rfy_..." \
     "https://reify.airgen.studio/api/v1/projects/<slug>/views?viewpoint=bdd"

# Single view metadata
curl -H "Authorization: Bearer rfy_..." \
     "https://reify.airgen.studio/api/v1/projects/<slug>/views/<viewId>"

# View-scoped SysML
curl -H "Authorization: Bearer rfy_..." \
     "https://reify.airgen.studio/api/v1/projects/<slug>/views/<viewId>/sysml"

# Structured diagram JSON
curl -H "Authorization: Bearer rfy_..." \
     "https://reify.airgen.studio/api/v1/projects/<slug>/views/<viewId>/diagram"
```

### Diagrams (direct by type)

```sh
curl -H "Authorization: Bearer rfy_..." \
     "https://reify.airgen.studio/api/v1/projects/<slug>/diagrams/bdd?scope=Power%20Subsystem"
```

`type` is one of `bdd`, `ibd`, `act`, `saf`, `req`, `stm`, `cxd`,
`uc`, `ffd`. Optional `scope` narrows to a subsystem / hazard.

### Requirements + trace links

```sh
# AIRGen passthrough — list requirements
curl -H "Authorization: Bearer rfy_..." \
     "https://reify.airgen.studio/api/v1/projects/<slug>/requirements?document=system-requirements"

# Single requirement
curl -H "Authorization: Bearer rfy_..." \
     "https://reify.airgen.studio/api/v1/projects/<slug>/requirements/SYS-001"

# Trace links — optional filter by ref or type
curl -H "Authorization: Bearer rfy_..." \
     "https://reify.airgen.studio/api/v1/projects/<slug>/trace-links?ref=SYS-001&type=derives"
```

### Raw substrate facts (escape hatch)

```sh
curl -H "Authorization: Bearer rfy_..." \
     "https://reify.airgen.studio/api/v1/projects/<slug>/facts?predicate=PART_OF&limit=200"
```

`limit` caps at 2000.

### Workspace and dry-run

```sh
# Workspace state — drift flag + full file contents
curl -H "Authorization: Bearer rfy_..." \
     "https://reify.airgen.studio/api/v1/projects/<slug>/workspace"

# Dry-run a SysML edit (parses, diffs, validates — applies nothing)
curl -X POST -H "Authorization: Bearer rfy_..." \
     -H "Content-Type: application/json" \
     -d '{"sysml": "package X { part def P { } }"}' \
     "https://reify.airgen.studio/api/v1/projects/<slug>/workspace/dry-run"
```

If `sysml` is omitted from the dry-run body, the substrate uses the
current workspace buffer.

### Audit

```sh
curl -H "Authorization: Bearer rfy_..." \
     "https://reify.airgen.studio/api/v1/projects/<slug>/audit"
```

`WORKSPACE_COMMIT` history, returned as
`{ items: [{ timestamp, raw, ... }] }`. The `raw` field is a
pipe-delimited summary line containing the SysML hash and the
substrate / requirements / links counts, e.g.
`2026-04-10T18:17:16.827Z | sha:14828a6776ef006d | substrate:+1/-0 | reqs:+0 | links:+1`.
Parse client-side if you need structured fields. Newest first.

## What v1 doesn't do

By design:

- **No write endpoints.** Commit, view CRUD, workspace file PUT,
  workspace reset, and agent triggers are all behind the cookie-
  authenticated UI-contract routes (`/api/projects/[slug]/...`).
- **No OpenAPI spec.** Hand-curated documentation for now; an
  OpenAPI spec will appear once there are multiple consumers.
- **No token management UI.** Token creation is the CLI script.
- **No pagination.** Lists wrap in `{ items }` so pagination can be
  added additively. Substrate fact query has a `limit` cap; others
  don't.
- **No rate limiting.** Single-tenant tooling for now.

## Patterns

### Render a diagram in your own UI

```ts
const r = await fetch(
  `${REIFY}/api/v1/projects/${slug}/diagrams/bdd?scope=${encodeURIComponent("Power Subsystem")}`,
  { headers: { Authorization: `Bearer ${token}` } }
);
const body = await r.json();
// Response shape: { type, scope, diagram: { nodes, edges } }
const { nodes, edges } = body.diagram;
// pass to your renderer (ReactFlow-shaped)
```

### Pre-commit validation in CI

```sh
# In CI, point to the SysML you intend to commit
curl -X POST \
  -H "Authorization: Bearer $REIFY_TOKEN" \
  -H "Content-Type: application/json" \
  -d "{\"sysml\": $(jq -Rs . < workspace.sysml)}" \
  "$REIFY/api/v1/projects/$SLUG/workspace/dry-run" \
  | jq '.parse_errors, .pre_commit_findings'
```

Fail the pipeline on any non-empty array.

### Generate a regulatory snapshot

Use `/sysml`, `/requirements`, `/trace-links`, `/facts`, and
`/audit` as a snapshot bundle:

```sh
mkdir -p snapshot
curl -H "Authorization: Bearer $T" \
  "$REIFY/api/v1/projects/$SLUG/sysml" > snapshot/source.sysml
curl -H "Authorization: Bearer $T" \
  "$REIFY/api/v1/projects/$SLUG/requirements" > snapshot/requirements.json
curl -H "Authorization: Bearer $T" \
  "$REIFY/api/v1/projects/$SLUG/trace-links" > snapshot/traces.json
curl -H "Authorization: Bearer $T" \
  "$REIFY/api/v1/projects/$SLUG/audit" > snapshot/audit.json
```

Tar it up, sign it, and ship.

## What's next

- [Using the Reify MCP server from Claude Desktop](./using-the-reify-mcp-server-from-claude-desktop.md)
- [Embedding `sysml-reactflow` in your own application](./embedding-sysml-reactflow-in-your-own-application.md)
- [Editing SysML v2 source: workspace, dry-run, and commit](./editing-sysml-v2-source-workspace-dry-run-and-commit.md)
