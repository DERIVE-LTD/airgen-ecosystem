# Ecosystem overview

AIRGen, Derive, and Reify are three independent products that together
form an integrated platform for AI-assisted, model-based systems
engineering in regulated industries.

```
┌───────────────────────────────────────────────────────────────────────┐
│                            STAKEHOLDER NEEDS                          │
└───────────────────────────────┬───────────────────────────────────────┘
                                │ (prose, documents, conversations)
                                ▼
                       ┌────────────────────┐
                       │      DERIVE        │  Autonomous SE workbench
                       │  (agent flows)     │  · decompose
                       │                    │  · enrich knowledge graph
                       │                    │  · validate, red-team
                       └─────────┬──────────┘
                                 │ writes candidates
                                 ▼
                       ┌────────────────────┐
                       │      AIRGEN        │  Requirements platform
                       │  (system of record)│  · QA scoring
                       │                    │  · trace links
                       │                    │  · documents, baselines
                       └─────────┬──────────┘
                                 │ provides structured data
                                 ▼
                       ┌────────────────────┐
                       │       REIFY        │  SysML v2 viewer + editor
                       │  (visualisation)   │  · 14 views per project
                       │                    │  · live SysML v2 editor
                       │                    │  · HTTP + MCP APIs
                       └────────────────────┘
```

## What each product owns

**AIRGen** is the system of record. It owns:

- The canonical store of requirements, documents, trace links, baselines,
  and verification activities.
- Deterministic QA scoring against ISO/IEC/IEEE 29148 and EARS rules.
- Tenant and project isolation, authentication, and audit history.
- Programmatic access — REST API, CLI, and Claude MCP server.

If a customer adopts only AIRGen, they get a complete requirements
engineering platform.

**Derive** is the autonomous workbench. It does not _own_ data — it
proposes data. It uses an orchestration engine of agent flows
(decompose, enrich, validate, red-team, and others) to:

- Read existing context from AIRGen and the knowledge-graph substrate.
- Run Claude-driven sessions to produce candidate artefacts.
- Write candidates back to AIRGen for human review.

The human-in-the-loop gate is intentional. In regulated contexts,
agent-drafted artefacts must be reviewed by a competent engineer before
they enter the live data set.

**Reify** is the visualisation layer. It reads each project's SysML
v2 source, parses it once, and renders it as fourteen views —
structural, behavioural, requirements, safety, and tabular. The
SysML editor is live: edit the source, switch to a diagram view, see
the change. Reify also exposes an HTTP API and an MCP server so the
same data is available to programmatic clients and AI agents. It is
built on top of `sysml-reactflow`, an open-source React library for
SysML v2 visualisation.

## How they integrate

| Boundary           | Mechanism                                                      |
| ------------------ | -------------------------------------------------------------- |
| Derive ↔ AIRGen   | AIRGen REST API. Derive authenticates as a tenant user.        |
| Reify ↔ AIRGen    | Reify reads each project's SysML v2 source (exported from the  |
|                    | AIRGen / Derive pipeline) and parses it client-side.           |
| Cross-app linking  | URL-based deep links between Derive sessions, AIRGen           |
|                    | requirements, and Reify diagrams (in progress).                |

## Adoption paths

You do not need all three to get value. Common adoption paths:

1. **AIRGen only** — for teams that want a modern requirements platform
   with QA scoring and traceability, without AI orchestration.
2. **AIRGen + Reify** — for teams that already have structured data and
   want SysML v2 visualisation without changing their authoring flow.
3. **Full ecosystem** — for teams adopting AI-assisted decomposition and
   enrichment from stakeholder needs through to SysML v2 models.

Self-hosted, hosted SaaS, and managed enterprise deployments are
available for each path. Contact [Derive Ltd](https://derive-ltd.co.uk)
to discuss your environment.

## Further reading

- [AIRGen documentation](../airgen/)
- [Derive documentation](../derive/)
- [Reify documentation](../reify/)
