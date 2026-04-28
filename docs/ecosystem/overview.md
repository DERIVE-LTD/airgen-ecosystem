# Ecosystem overview

The AIRGen ecosystem is five components working as one pipeline for
AI-assisted, model-based systems engineering in regulated industries.

```
                                ┌──────────┐
                                │   USER   │
                                └─────┬────┘
                                      │ OAuth (browser)
                                      ▼
                       ┌─────────────────────────────┐
                       │            DERIVE           │  Operator workbench
                       │   The only UI to the loop   │  · dashboard
                       │                             │  · journal
                       │                             │  · quality view
                       │                             │  · loop controls
                       └────────┬──────────────┬─────┘
                                │              │
                       controls │              │ reads back
                                ▼              │
                       ┌─────────────────┐     │
                       │  CLAUDE HARNESS │     │
                       │   Autonomous    │     │
                       │ engine; no UI   │     │
                       │ · concept → ... │     │
                       │ · ... → review  │     │
                       └────┬────────┬───┘     │
                            │        │         │
                            │        │         │
                     writes │        │ writes  │
                     reqs + │        │ facts   │
                     traces │        │ (SE:    │
                            │        │ slug)   │
                            ▼        ▼         │
                   ┌─────────────┐  ┌──────────────────┐
                   │   AIRGEN    │  │  UHT SUBSTRATE   │
                   │ System of   │  │  Knowledge graph │ ◀── Derive reads
                   │ record      │  │ · 32-bit taxonomy│      both stores
                   │ · QA / EARS │  │ · namespaced     │      to render its
                   │ · trace     │  │   facts          │      views
                   │ · baselines │  │ · MCP/REST/CLI   │
                   └──────┬──────┘  └────────┬─────────┘
                          │                  │
                          └────────┬─────────┘
                                   │ both read by
                                   ▼
                       ┌─────────────────────────────┐
                       │            REIFY            │  SysML v2 viewer
                       │   Reads AIRGen + Substrate, │  · 14 views
                       │   reconstructs SysML v2,    │  · live editor
                       │   renders 14 views          │  · HTTP + MCP API
                       └─────────────────────────────┘
```

The harness has no user interface of its own. Every operator action —
pause the loop, unpause, submit a directive, review what was produced
— happens through Derive. The harness writes to AIRGen, UHT Substrate,
and the journal filesystem; Derive reads back from all three to render
the operator view; Reify reads AIRGen and UHT Substrate to render the
SysML v2 view.

## What each component owns

### AIRGen — system of record

AIRGen is the canonical home for requirements, documents, trace links,
baselines, and verification activities. It enforces deterministic QA
scoring against ISO/IEC/IEEE 29148 and EARS rules, and exposes its data
through a REST API, a CLI, and a Claude MCP server. Tenant and project
isolation, JWT + 2FA authentication, and audit history live here too.

If a customer adopts only AIRGen, they get a complete requirements
engineering platform.

### UHT Substrate — knowledge graph

UHT Substrate is the fact store and semantic-reasoning engine. It
classifies any concept into a 32-bit hex code across physical,
functional, abstract, and social layers, and uses that classification
for similarity, search, inference, and structured fact storage.

For systems-engineering projects, the convention is to namespace facts
as `SE:<project-slug>`. The autonomous loop writes part-of and other
structural facts there; Reify reconstructs SysML v2 from them; Derive
surfaces them via the spec-tree.

### Claude Harness — autonomous engine

The harness is the orchestration layer that runs Claude-driven sessions
through a config-driven state machine. Each session is one phase of a
multi-template pipeline (concept, scaffold, decompose, QC, validate,
review), and writes its output into AIRGen and UHT Substrate plus a
journal post for human review.

The harness has **no UI of its own**. Every operator interaction goes
through Derive: pause and unpause, directives, and review of what each
session produced. Its source is private; this docs hub describes only
its public-facing role within the ecosystem.

### Derive — operator workbench

Derive is the cockpit. It does not own data — it reads from AIRGen,
UHT Substrate, SurrealDB (for harness state), and the journal
filesystem, and presents a per-project workspace: spec-tree dashboard,
session journal, quality view, report viewer, baseline and artefact
exports, and the controls (`/control`, `/p/<slug>/control`) for
pausing, unpausing, and directing the harness loop.

The human-in-the-loop gate is intentional. In regulated contexts,
agent-drafted artefacts must be reviewed by a competent engineer
before they enter the live data set.

### Reify — SysML v2 visualisation

Reify reads each project's data directly from **AIRGen** (requirements,
trace links) and **UHT Substrate** (facts) and reconstructs the
canonical SysML v2 source from both. It then renders fourteen views —
structural, behavioural, requirements, safety, and tabular. The SysML
editor is live: edit the source, switch to a diagram view, see the
change. Reify also exposes an HTTP API and an MCP server, and is built
on top of `sysml-reactflow`, an open-source React library for SysML v2
visualisation.

## How they integrate

| Boundary                       | Mechanism                                                                  |
| ------------------------------ | -------------------------------------------------------------------------- |
| Harness ↔ AIRGen              | AIRGen REST API — the harness writes requirements and trace links.         |
| Harness ↔ UHT Substrate       | Substrate REST / MCP — the harness writes namespaced facts.                |
| Harness ↔ Derive              | The harness writes journal markdown and SurrealDB session records that Derive reads. The harness has **no direct UI** — Derive is the operator interface. |
| Derive ↔ AIRGen               | AIRGen REST API for projects, requirements, baselines.                     |
| Derive ↔ UHT Substrate        | Substrate REST API for the spec-tree and namespace queries.                |
| Reify ↔ AIRGen                | Reify reads requirements and trace links via the AIRGen REST API.          |
| Reify ↔ UHT Substrate         | Reify reads namespaced facts via the Substrate REST API and reconstructs the canonical SysML v2 source from AIRGen + Substrate combined. |
| Cross-app linking              | URL-based deep links between Derive, AIRGen, and Reify.                    |

## Adoption paths

You do not need all five components to get value:

1. **AIRGen only** — for teams that want a modern requirements platform
   with QA scoring and traceability, without AI orchestration.
2. **AIRGen + Reify** — for teams that already have structured data and
   want SysML v2 visualisation without changing their authoring flow.
3. **AIRGen + UHT Substrate + Reify** — for teams adopting the
   knowledge-graph layer to enable semantic search and inference, plus
   SysML v2 visualisation.
4. **Full ecosystem** — for teams adopting AI-assisted decomposition
   and enrichment from stakeholder needs through to SysML v2 models,
   with Derive as the operator interface.

Self-hosted, hosted SaaS, and managed enterprise deployments are
available for each path. Contact [Derive Ltd](https://derive-ltd.co.uk)
to discuss your environment.

## Further reading

- [AIRGen documentation](../airgen/)
- [Derive documentation](../derive/)
- [Reify documentation](../reify/)
- [UHT Substrate documentation](../uht-substrate/)
- [Claude Harness documentation](../claude-harness/)
