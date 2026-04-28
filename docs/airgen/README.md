# AIRGen

**AI-powered requirements engineering platform for regulated systems.**

AIRGen combines deterministic QA scoring (ISO/IEC/IEEE 29148, EARS) with
AI-assisted drafting, and maintains complete traceability through a
Neo4j graph database. It is delivered as a web application, a CLI, and a
Model Context Protocol (MCP) server for use from AI agents.

- **Hosted:** [airgen.studio](https://airgen.studio)
- **Self-hosted:** Docker Compose stack — Fastify API, React frontend,
  Neo4j 5, PostgreSQL 16, Redis 7, fronted by Traefik with TLS
- **Available as:** SaaS, self-hosted, or managed enterprise deployment
- **Published packages:**
  - [`@derive-ltd/airgen-cli`](https://www.npmjs.com/package/@derive-ltd/airgen-cli) — terminal client _(supersedes the unscoped `airgen-cli`)_
- **Licence:** MIT

## What AIRGen does

| Capability                | Summary                                                                                  |
| ------------------------- | ---------------------------------------------------------------------------------------- |
| Requirements management   | Authoring, tagging, archiving, advanced filtering, inline editing, immutable version history. |
| Deterministic QA scoring  | 0–100 quality score against ISO/IEC/IEEE 29148 and EARS pattern rules.                   |
| Background QA worker      | Score every requirement in a project automatically, with real-time progress.             |
| AI-assisted drafting      | Generate candidate requirements from natural-language descriptions.                      |
| Document management       | Native structured documents plus surrogate uploads (Word, PDF, …) with parsing.          |
| Typed trace links         | `satisfies`, `derives`, `verifies`, `implements`, `refines`, `conflicts`.                |
| Linksets                  | Organise related trace links into structured collections.                                |
| Architecture diagrams     | Interactive ReactFlow canvas with blocks, ports, connectors, and floating windows.       |
| Imagine                   | AI image generation for concept visualisation, saveable as surrogate documents.          |
| Verification engine       | Test, analysis, inspection, and demonstration activities with evidence attachment.       |
| Baselines                 | Point-in-time snapshots with diffing for release management.                             |
| Multi-tenant              | Tenant and project isolation, role-based access, JWT + 2FA + Argon2id.                   |

## Programmatic access

### CLI — `@derive-ltd/airgen-cli`

Most commands take `<tenant> <project>` as positional arguments.

```sh
npm install -g @derive-ltd/airgen-cli

# Configure via ~/.airgenrc or env vars

airgen tenants list
airgen projects list <tenant>
airgen reqs list <tenant> <project>
airgen reqs create <tenant> <project> \
    --text "The system shall..." \
    --document system-requirements
airgen baselines create <tenant> <project> --name "v1.0"
airgen traces list <tenant> <project>
airgen quality score <tenant> <project>
airgen export csv <tenant> <project>
```

### REST API

Routes are served under the `/api` prefix. Most resource paths follow
`/<resource>/:tenant/:project/...`. Interactive Swagger docs at
`/api/docs` when running.

```sh
curl https://airgen.studio/api/requirements/<tenant>/<project>
```

Top-level groups: auth, tenants, projects, requirements, documents,
trace links, architecture, baselines, verification, AI, imagine,
health.

### MCP server

The MCP server exposes AIRGen as **70 tools across 18 modules** —
`navigation`, `project-management`, `requirements`, `documents`,
`document-management`, `section-content`, `quality`, `traceability`,
`baselines`, `architecture`, `search`, `filtering`, `ai`, `imagine`,
`activity`, `implementation`, `reporting`, `import-export`.

## Backup and recovery

AIRGen runs two complementary automated backup tracks, both uploading
to a [restic](https://restic.net) remote (DigitalOcean Spaces, S3, B2,
SFTP, or local disk) with client-side AES-256 encryption.

| Track            | Scope                                                                | Schedule                                |
| ---------------- | -------------------------------------------------------------------- | --------------------------------------- |
| Full-database    | Neo4j tar.gz, PostgreSQL `pg_dump`, config — disaster recovery.       | Daily 02:00 UTC + weekly Sunday 03:00 UTC. Verify run at 02:30 UTC. |
| Per-project      | Single project as a self-contained Cypher script + remote upload, registered in a `project_backups` catalog. | Daily 04:00 UTC + weekly Sunday 05:00 UTC (parallelised). |

## Guides

Start here:

- [Getting started](./getting-started.md) — first-run walkthrough

In-depth guides:

- [Authoring your first requirement](./guides/authoring-your-first-requirement.md)
- [Understanding the QA score and EARS patterns](./guides/understanding-the-qa-score-and-ears-patterns.md)
- [Building a traceability matrix](./guides/building-a-traceability-matrix.md)
- [Using the `@derive-ltd/airgen-cli` CLI](./guides/using-the-airgen-cli.md)
- [Connecting AIRGen to Claude via the MCP server](./guides/connecting-airgen-to-claude-via-the-mcp-server.md)
- [Self-hosting AIRGen with Docker Compose](./guides/self-hosting-airgen-with-docker-compose.md)
- [Configuring the two-track backup model and restoring from restic](./guides/configuring-the-two-track-backup-model.md)
