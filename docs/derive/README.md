# Derive

**Autonomous systems-engineering workbench.**

Derive sits on top of AIRGen and orchestrates AI agents to perform the
labour-intensive parts of systems engineering: requirements
decomposition, knowledge-graph enrichment, validation, and report
generation. Where AIRGen is the system of record, Derive is the place
where _work_ happens.

- **Hosted:** [derive.airgen.studio](https://derive.airgen.studio)
- **Backed by:** the AIRGen API, the `uht-substrate` knowledge graph,
  and Claude-driven agent flows

## What Derive does

| Capability                  | Summary                                                                     |
| --------------------------- | --------------------------------------------------------------------------- |
| Requirements decomposition  | Break stakeholder needs into system, subsystem, and interface requirements. |
| Knowledge-graph enrichment  | Extract structured facts (functions, components, modes, hazards) from prose.|
| Traceability authoring      | Generate and review typed trace links between requirements.                 |
| Reports                     | Produce structured engineering reports from project data.                   |
| Agent flows                 | Pre-built flows for concept, decomposition, MBSE expert, QC, red-team, review, scaffold, technical author, and validation. |

## Guides

- [Getting started](./getting-started.md) — first-run walkthrough
- [`guides/`](./guides/) — task-oriented guides _(coming soon)_

Planned topics:

- Decomposing your first stakeholder need
- Understanding agent flows
- Reviewing and accepting agent-generated artefacts
- Wiring Derive to a self-hosted AIRGen
- Custom flow definitions
