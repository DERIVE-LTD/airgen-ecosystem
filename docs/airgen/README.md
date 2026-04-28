# AIRGen

**AI-powered requirements engineering platform for regulated systems.**

AIRGen combines deterministic QA scoring (ISO/IEC/IEEE 29148, EARS) with
AI-assisted drafting, and maintains complete traceability through a
Neo4j graph database. It is delivered as a web application, a CLI, and a
Model Context Protocol (MCP) server for use from AI agents.

- **Hosted:** [airgen.studio](https://airgen.studio)
- **Self-hosted:** Docker Compose stack — Fastify API, React frontend, Neo4j, PostgreSQL, Redis
- **Available as:** SaaS, self-hosted, or managed enterprise deployment

## What AIRGen does

| Capability                | Summary                                                                                  |
| ------------------------- | ---------------------------------------------------------------------------------------- |
| Requirements management   | Authoring, tagging, archiving, advanced filtering, inline editing, full version history. |
| Deterministic QA scoring  | 0–100 quality score against ISO/IEC/IEEE 29148 and EARS pattern rules.                   |
| AI-assisted drafting      | Generate candidate requirements from natural-language descriptions.                      |
| Document management       | Native structured documents plus surrogate uploads (Word, PDF, …) with parsing.          |
| Typed trace links         | `satisfies`, `derives`, `verifies`, `implements`, `refines`, `conflicts`.                |
| Architecture diagrams     | Interactive ReactFlow canvas with blocks, ports, and connectors.                         |
| Verification engine       | Test, analysis, inspection, and demonstration activities with evidence attachment.       |
| Baselines                 | Point-in-time snapshots with diffing for release management.                             |
| Multi-tenant              | Tenant and project isolation, role-based access, JWT + 2FA.                              |

## Guides

- [Getting started](./getting-started.md) — first-run walkthrough
- [`guides/`](./guides/) — task-oriented guides _(coming soon)_

Planned topics:

- Authoring your first requirement
- Understanding the QA score
- Building a traceability matrix
- Using the AIRGen CLI
- Connecting AIRGen to Claude via the MCP server
- Self-hosting AIRGen with Docker Compose
- Configuring backups and disaster recovery
