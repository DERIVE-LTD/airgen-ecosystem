# Ten things to ask Claude about your projects

A guided tour of the AIRGen ecosystem from inside Claude Desktop. Three
of the five components — **AIRGen**, **Reify**, and **UHT Substrate** —
each expose an MCP server. Wire all three into Claude and you get an
interactive surface across requirements, traceability, the SysML
model, and the underlying knowledge graph.

This page walks through ten concrete prompts. Each one names the MCP
servers and tools it touches so you can see what's happening
underneath.

> **Audience:** anyone using Claude Desktop (or another MCP client) who
> wants to interact with their AIRGen / Derive / Reify projects without
> opening the browser. Operators still drive the autonomous loop from
> [Derive](../derive/) — that part has no MCP surface.

## Setup — wire the MCP servers into Claude Desktop

Each of the three MCP-enabled components has a different wiring story
today. Reify is published as a ready-to-go npm package; AIRGen's MCP
server is in-tree but not yet on npm; UHT Substrate's MCP server is
the Python service itself. The configurations below reflect that.

Add the relevant blocks to `claude_desktop_config.json`
(`~/Library/Application Support/Claude/claude_desktop_config.json` on
macOS, `%APPDATA%\Claude\claude_desktop_config.json` on Windows).

### Reify — ready, via npm

The `@derive-ltd/reify` package ships an MCP stdio server (the
`reify-mcp` bin), tested with Claude Desktop:

```json
{
  "mcpServers": {
    "reify": {
      "command": "npx",
      "args": ["-y", "@derive-ltd/reify"],
      "env": {
        "REIFY_API_URL": "https://reify.airgen.studio",
        "REIFY_API_TOKEN": "rfy_paste_your_token_here"
      }
    }
  }
}
```

Token generation is described in the
[Reify documentation](../reify/).

### AIRGen — local build for now

The AIRGen MCP server lives in
[`packages/mcp-server`](https://github.com/Hollando78/airgen/tree/main/packages/mcp-server)
of the AIRGen repo but **is not yet published to npm**. To wire it
into Claude Desktop today, build the source locally and point
Claude Desktop at the resulting binary:

```json
{
  "mcpServers": {
    "airgen": {
      "command": "node",
      "args": ["/absolute/path/to/airgen/packages/mcp-server/dist/index.js"],
      "env": {
        "AIRGEN_API_URL": "https://api.airgen.studio/api",
        "AIRGEN_API_KEY": "your-airgen-api-key"
      }
    }
  }
}
```

Required environment:

- `AIRGEN_API_URL` — base URL (note the `api.` subdomain on production:
  `https://api.airgen.studio/api`)
- Either `AIRGEN_API_KEY` (preferred), or `AIRGEN_EMAIL` +
  `AIRGEN_PASSWORD` for password-based login

The CLI package `@derive-ltd/airgen-cli` (or its legacy unscoped name
`airgen-cli`) is a **REST/CLI client only** — it does not embed an
MCP server.

### UHT Substrate — Python install

UHT Substrate's MCP server is the Python service itself. Self-host
the substrate, then point Claude Desktop at the resulting binary:

```sh
# from the substrate source
pip install -e .
```

Then in `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "uht-substrate": {
      "command": "/absolute/path/to/.venv/bin/uht-substrate",
      "env": {
        "UHT_NEO4J_PASSWORD": "...",
        "UHT_API_BASE_URL": "https://factory.universalhex.org/api/v1",
        "UHT_API_KEY": "your-factory-token"
      }
    }
  }
}
```

`uht-substrate` runs in MCP mode by default; pass `--web` instead to
serve REST + MCP over HTTP. The npm package
`@derive-ltd/uht-substrate` is a REST CLI client and does not embed
an MCP server.

### Verifying the wiring

Restart Claude Desktop. You should see new tools in the tool picker:

- **`reify`** — ~19 read-only tools (verified)
- **`airgen`** — 70 tools across 18 modules (once built and wired)
- **`uht-substrate`** — classification, entity, fact, namespace tools
  (once Python install is wired)

Wiring details can move — verify against the upstream READMEs
([airgen](https://github.com/Hollando78/airgen),
[reify](https://github.com/Hollando78/reify),
[uht-substrate](https://github.com/Hollando78/uht-substrate)) before
quoting these blocks.

## The tour

### 1. "What projects can I see?"

> *"What projects do I have access to in Reify?"*

Touches: `reify_list_projects`.

Cheapest possible discovery prompt — confirms the MCP wiring works and
gives you a list of slugs to use in later prompts.

### 2. "Give me a summary of <project>"

> *"Summarise the EEG Accessibility Buggy project — its subsystems,
> stakeholder counts, and current workspace state."*

Touches: `reify_get_project`, `reify_get_stats`.

Returns the project's metadata, count summary, and workspace state —
including whether the editor buffer has drifted from the canonical
SysML. A good orientation prompt before you dig deeper.

### 3. "List the requirements in <document>, with QA scores"

> *"List all requirements in the system-requirements document of
> tenant `acme`, project `widget-x`, sorted by QA score ascending,
> and show me the lowest five."*

Touches: AIRGen `requirements` and `quality` modules
(`reqs list`, `quality score`).

This is the same workflow you'd run from `airgen reqs list` on the
CLI, but from inside Claude. Useful for "where's the work?" — sorting
by QA score surfaces the requirements most in need of rewriting.

### 4. "Show me the Block Definition Diagram for the Power Subsystem"

> *"Render the BDD diagram for project `widget-x` scoped to the Power
> Subsystem."*

Touches: `reify_get_view_diagram` (or `reify_get_diagram` with
`type=bdd`, `scope=Power Subsystem`).

Returns a structured JSON description of the diagram — nodes, edges,
positions. Claude can summarise it in prose, list the components, or
just hand you the JSON for downstream tooling.

### 5. "Which requirements have no trace links?"

> *"Find every requirement in `acme/widget-x` that has no incoming or
> outgoing trace links and group them by document."*

Touches: AIRGen `requirements`, `traceability`, and `filtering`
modules.

Orphan detection is one of the highest-value automated checks in
requirements engineering. Doing it from Claude lets you immediately
follow up with *"draft three candidate trace links for this orphan"* —
which is example 8.

### 6. "Classify 'thermal radiator' and find similar entities"

> *"Classify the entity 'thermal radiator' in the UHT taxonomy and
> show me the five most similar entities in the corpus."*

Touches: Substrate `classify_entity`, `find_similar_entities`.

Returns the 8-character hex code, the 32-trait breakdown, and a
ranked list of nearest neighbours. Useful for naming/disambiguation
when you're modelling a new system, or sanity-checking that a
component's classification is sensible.

### 7. "Show me hazards with their mitigating requirements"

> *"For project `widget-x`, list every hazard, the requirements that
> mitigate it, and the verification activities for each requirement."*

Touches: Substrate `query_facts` (e.g. `HAS_HAZARD` predicates),
AIRGen `requirements`, `traceability`, and `verification` modules.

This is a genuinely cross-server query — hazards live as substrate
facts; the mitigating relationship is typically an AIRGen trace link
(`satisfies` or `verifies`) between the requirement and the hazard;
verification activities live in AIRGen too. Claude resolves the join.
The same view in the browser is Reify's `/saf` (safety) diagram.

### 8. "Draft candidate requirements from this stakeholder need"

> *"For tenant `acme`, project `widget-x`, draft five candidate system
> requirements that satisfy this stakeholder need: 'Operators must be
> able to power-cycle the unit remotely without losing telemetry
> continuity.' Use EARS where appropriate."*

Touches: AIRGen `ai` module (`airgen draft` / `candidates`).

The output is structured candidates that go into AIRGen as
human-reviewable drafts. This is the classic AI-drafting flow, but
from inside Claude — useful when you're already in a conversation
fleshing out the system concept.

### 9. "Compare baselines v1.0 and v1.1"

> *"Show me everything that changed between baselines `v1.0` and
> `v1.1` of project `widget-x` — added, removed, modified
> requirements, and any trace-link deltas."*

Touches: AIRGen `baselines` module (`baselines compare`).

Release-gate workflow. You can follow up with *"now generate a
release-note bullet list summarising those changes"* and Claude will
turn the raw delta into prose.

### 10. "Reconcile substrate facts against AIRGen trace links"

> *"Reconcile the substrate facts under namespace `SE:widget-x`
> against AIRGen trace links and report any inconsistencies — facts
> referencing requirements that don't exist, trace links that don't
> match an underlying fact, that sort of thing."*

Touches: Substrate `reconcile_facts` (cross-checks the substrate
namespace against AIRGen trace links).

This is the consistency check that keeps the knowledge graph and the
requirements-of-record in sync. Worth running periodically — and a
natural last stop on the tour because it touches both stores by
design.

## Patterns that emerge

A few patterns are worth pointing out:

- **Discovery → focus → action.** Most useful sessions start with a
  list (projects, requirements, facts), narrow to a single artefact,
  and end with either a write (drafting, candidate creation) or a
  report.
- **Cross-server joins are the killer feature.** AIRGen owns
  requirements and trace links; Substrate owns facts; Reify reads
  both and renders SysML. Asking Claude a question that crosses two
  of those is something the browser UI can't easily replicate — it's
  the strongest argument for the MCP path.
- **Read-only is the default.** Reify is fully read-only over MCP.
  AIRGen and Substrate expose write tools, but in practice you can do
  most exploration without ever leaving the read surface. Save the
  writes for when you've decided what you want to commit.

## What's deliberately not exposed

- The **Claude Harness** has no MCP surface. The autonomous loop is
  driven only from Derive's loop control panel — there is no "tell
  Claude to start a harness session" tool. This is by design; the
  harness's behaviour is defined by its config, not by ad-hoc agent
  prompts.
- **Derive itself** has no MCP surface. Derive is a UI over data that
  is already exposed elsewhere — anything Derive shows is reachable
  via AIRGen, Reify, or Substrate MCP.
- **Writes through Reify** are kept behind the cookie-authenticated UI
  routes. Reify's `/mcp` and `/api/v1` are read-only by design, so
  agents cannot mutate the model directly through Reify.

## Further reading

- [AIRGen MCP server](../airgen/) — 70 tools across 18 modules
- [Reify MCP server](../reify/) — ~19 read-only tools at `/mcp`
- [UHT Substrate MCP server](../uht-substrate/) — classification,
  entity, fact, and namespace tools
- [Ecosystem overview](./overview.md)
