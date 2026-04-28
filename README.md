# AIRGen Ecosystem

Technical guides for the [Derive Ltd](https://derive-ltd.co.uk) product suite —
**AIRGen**, **Derive**, and **Reify** — an integrated platform for
AI-assisted, model-based systems engineering in regulated industries.

This repository is the canonical home for hands-on technical documentation.
For product overviews, contact information, and commercial enquiries, see
[derive-ltd.co.uk](https://derive-ltd.co.uk).

## The products

| Product   | One-liner                                                                                          | Hosted at                                          | Docs                                       |
| --------- | -------------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------------------------------------------ |
| **AIRGen** | AI-powered requirements engineering platform — QA scoring, traceability, diagrams, drafting.       | [airgen.studio](https://airgen.studio)             | [`docs/airgen/`](docs/airgen/)             |
| **Derive** | Autonomous systems-engineering workbench — requirements decomposition, reports, and traceability.   | [derive.airgen.studio](https://derive.airgen.studio) | [`docs/derive/`](docs/derive/)             |
| **Reify**  | SysML v2 model viewer and editor — fourteen views with live editor, HTTP + MCP APIs.                | [reify.airgen.studio](https://reify.airgen.studio) | [`docs/reify/`](docs/reify/)               |

For how the three fit together, see the [ecosystem overview](docs/ecosystem/overview.md).

## Repository layout

```
docs/
├── airgen/      Guides for AIRGen
├── derive/      Guides for Derive
├── reify/       Guides for Reify
└── ecosystem/   Cross-cutting topics — how the products integrate
```

Each product directory contains:

- `README.md` — product overview and a guide index
- `getting-started.md` — first-run walkthrough
- `guides/` — task-oriented technical guides

## Contributing

Issues and pull requests are welcome. For substantive changes please open
an issue first to discuss scope. We particularly welcome:

- Real-world usage examples and case studies
- Corrections and clarifications
- Translations
- Integration recipes for adjacent tools (DOORS, Jama, Polarion, etc.)

## Licence

Documentation in this repository is licensed under the
[Creative Commons Attribution 4.0 International Licence (CC BY 4.0)](LICENSE).
You are free to share and adapt the material for any purpose, including
commercially, provided you give appropriate credit.
