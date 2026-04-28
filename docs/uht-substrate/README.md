# UHT Substrate

**Semantic reasoning engine and fact store for the Universal Hex Taxonomy.**

UHT Substrate classifies any concept into a 32-bit trait vector
(an 8-character hex code) across four semantic layers — physical,
functional, abstract, and social — and uses that classification for
comparison, search, semantic inference, and structured fact storage.
For the AIRGen / Derive / Reify pipeline, it is the project-scoped
knowledge graph that holds entities and facts (`PART_OF`, `SPEC_TREE`,
`HAS_HAZARD`, `CONNECTS`, …) alongside the AIRGen requirements model.

- **Hosted:** [substrate.universalhex.org](https://substrate.universalhex.org)
- **Operator:** [Derive Ltd](https://derive-ltd.co.uk) (Company 17159546, England & Wales)
- **Stack:** Python (FastAPI), Neo4j, Pydantic, OAuth (Google / GitHub)
- **Published:** [`@derive-ltd/uht-substrate`](https://www.npmjs.com/package/@derive-ltd/uht-substrate) (CLI)
- **Licence:** MIT

## What UHT Substrate does

| Capability                  | Summary                                                                       |
| --------------------------- | ----------------------------------------------------------------------------- |
| Semantic classification     | Map any concept to an 8-character hex code over 32 binary traits.             |
| Entity comparison           | Jaccard similarity over trait vectors, with shared/unique trait breakdowns.   |
| Semantic search             | Search across the 16k+ Factory entity corpus.                                 |
| Disambiguation              | Word-sense breakdowns for polysemous terms.                                   |
| Inference                   | Property inference from classification via trait axioms.                      |
| Fact store                  | Subject-predicate-object facts, scoped by namespace.                          |
| Reconciliation              | Cross-check substrate facts against AIRGen trace links.                       |
| Entity lifecycle            | Update, reclassify, merge, history, and namespace-deduplicate operations on entities. |
| Three interfaces            | MCP server (for AI agents), REST API (for apps), CLI (for terminals).         |

## The 32-bit taxonomy

Every entity is evaluated against 32 binary traits across four layers:

| Layer        | Bits    | Examples                                                          |
| ------------ | ------- | ----------------------------------------------------------------- |
| Physical     | 1–8     | Physical Object, Synthetic, Biological, Powered                   |
| Functional   | 9–16    | Intentionally Designed, State-Transforming, System-Integrated     |
| Abstract     | 17–24   | Symbolic, Rule-Governed, Compositional, Temporal                  |
| Social       | 25–32   | Social Construct, Regulated, Economically Significant             |

Two entities with similar hex codes share similar properties.

## Programmatic access

### CLI

```sh
npm install -g @derive-ltd/uht-substrate

uht-substrate config set api-url https://substrate.universalhex.org/api
uht-substrate config set token YOUR_TOKEN

# Classification & search
uht-substrate classify "hammer" --format pretty
uht-substrate compare hammer screwdriver
uht-substrate search "hand tools" --limit 10

# Facts
uht-substrate facts store "spark plug" PART_OF "engine" --namespace SE:automotive
uht-substrate facts reconcile --namespace SE:automotive

# Entity lifecycle
uht-substrate entities update "spark plug" --description "Ignition component"
uht-substrate entities reclassify "spark plug" --context "internal combustion engine"
uht-substrate entities merge "old name" "canonical name"
uht-substrate entities history "spark plug"
uht-substrate entities deduplicate --namespace SE:automotive
```

### REST API

```sh
curl -X POST https://substrate.universalhex.org/api/classify \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"entity": "hammer"}'
```

Interactive endpoint reference at
[substrate.universalhex.org/api/docs](https://substrate.universalhex.org/api/docs).

### MCP server

Connect from Claude Desktop or any other MCP client to access the same
classification, search, entity, and fact tools used by the CLI and REST
API.

## Role in the ecosystem

UHT Substrate is the knowledge-graph layer that sits underneath
[Derive](../derive/) and [Reify](../reify/). When the autonomous loop
processes a project, it writes substrate facts (and AIRGen
requirements) keyed by a project namespace such as `SE:my-project`.
Reify reconstructs SysML v2 from those facts; Derive surfaces them
via the spec-tree, dashboard, and quality view.

## Guides

Start here:

- [Getting started](./getting-started.md) — first classification,
  comparison, and fact

In-depth guides:

- [Classifying entities for a new project](./guides/classifying-entities-for-a-new-project.md)
- [Storing and querying facts with namespace scoping](./guides/storing-and-querying-facts-with-namespace-scoping.md)
- [Reconciling substrate facts against AIRGen trace links](./guides/reconciling-substrate-facts-against-airgen-trace-links.md)
- [Entity lifecycle: reclassify, merge, history, deduplicate](./guides/entity-lifecycle.md)
- [Connecting the substrate MCP server to Claude Desktop](./guides/connecting-the-substrate-mcp-server-to-claude-desktop.md)
- [Self-hosting UHT Substrate with Neo4j](./guides/self-hosting-uht-substrate-with-neo4j.md)
