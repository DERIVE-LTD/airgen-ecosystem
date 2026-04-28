# AIRGen Ecosystem

Technical guides for the [Derive Ltd](https://derive-ltd.co.uk)
autonomous-systems-engineering platform — five components, one
pipeline.

This repository is the canonical home for hands-on technical
documentation. For product overviews, contact information, and
commercial enquiries, see [derive-ltd.co.uk](https://derive-ltd.co.uk).

## The components

The platform is built around three user-facing applications and two
infrastructure layers:

### User-facing applications

| Component  | One-liner                                                                                          | Hosted at                                              | Docs                                       |
| ---------- | -------------------------------------------------------------------------------------------------- | ------------------------------------------------------ | ------------------------------------------ |
| **AIRGen** | AI-powered requirements engineering platform — QA scoring, traceability, diagrams, drafting.       | [airgen.studio](https://airgen.studio)                 | [`docs/airgen/`](docs/airgen/)             |
| **Derive** | Operator workbench — spec-tree dashboard, journal, quality view, loop controls.                    | [derive.airgen.studio](https://derive.airgen.studio)   | [`docs/derive/`](docs/derive/)             |
| **Reify**  | SysML v2 model viewer and editor — fourteen views, live editor, HTTP + MCP APIs.                   | [reify.airgen.studio](https://reify.airgen.studio)     | [`docs/reify/`](docs/reify/)               |

### Infrastructure

| Component         | One-liner                                                                                  | Hosted at                                                          | Docs                                                 |
| ----------------- | ------------------------------------------------------------------------------------------ | ------------------------------------------------------------------ | ---------------------------------------------------- |
| **UHT Substrate** | Semantic-reasoning engine and namespaced fact store — the project knowledge graph.         | [substrate.universalhex.org](https://substrate.universalhex.org)   | [`docs/uht-substrate/`](docs/uht-substrate/)         |
| **Claude Harness** | Autonomous Claude Code session orchestrator — runs the loop behind Derive. _(Source private.)_ | _(internal)_                                                       | [`docs/claude-harness/`](docs/claude-harness/)       |

For how the five fit together, see the
[ecosystem overview](docs/ecosystem/overview.md). For an interactive
tour from inside Claude Desktop, see
[ten things to ask Claude about your projects](docs/ecosystem/mcp-tour.md).

## Repository layout

```
docs/
├── airgen/         Guides for AIRGen
├── derive/         Guides for Derive
├── reify/          Guides for Reify
├── uht-substrate/  Guides for UHT Substrate
├── claude-harness/ Guides for the Claude Harness (operator-facing)
└── ecosystem/      Cross-cutting topics — how the components integrate
```

Each component directory contains:

- `README.md` — overview and a guide index
- `getting-started.md` — first-run walkthrough
- `guides/` — task-oriented technical guides

## Contributing

Issues and pull requests are welcome. For substantive changes please
open an issue first to discuss scope. We particularly welcome:

- Real-world usage examples and case studies
- Corrections and clarifications
- Translations
- Integration recipes for adjacent tools (DOORS, Jama, Polarion, etc.)

## Licence

Documentation in this repository is licensed under the
[Creative Commons Attribution 4.0 International Licence (CC BY 4.0)](LICENSE).
You are free to share and adapt the material for any purpose, including
commercially, provided you give appropriate credit.
