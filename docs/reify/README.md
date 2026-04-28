# Reify

**SysML v2 model viewer and editor.**

Reify reads requirements and trace links from [AIRGen](../airgen/) and
namespaced facts from [UHT Substrate](../uht-substrate/), reconstructs
the canonical SysML v2 source from both, and renders structural,
behavioural, and requirements diagrams. It pairs a CodeMirror editor
for the SysML text with React Flow + ELK auto-layout for the diagrams,
and exposes the same data through both an HTTP API and an MCP server
for programmatic and agentic access.

- **Hosted:** [reify.airgen.studio](https://reify.airgen.studio)
- **Public demo:** [reify.airgen.studio/demo](https://reify.airgen.studio/demo)
- **Stack:** Next.js 16 (App Router) on React 19, Tailwind 4,
  CodeMirror 6, React Flow 12, ELK.js, Mermaid 11
- **Published packages:**
  - [`sysml-reactflow`](https://www.npmjs.com/package/sysml-reactflow) — open-source React component library for SysML v2 ([live Storybook](https://hollando78.github.io/SysML-reactflow/))
  - [`@derive-ltd/reify`](https://www.npmjs.com/package/@derive-ltd/reify) — MCP server (`reify-mcp`) and CLI (`reify`)

## What Reify does

| Capability                  | Summary                                                              |
| --------------------------- | -------------------------------------------------------------------- |
| Live SysML v2 editor        | Edit a multi-file SysML source in CodeMirror, watch diagrams re-render. |
| Twelve views                | One canonical diagram per system aspect, plus a tabular view and the SysML text editor. |
| ELK auto-layout             | Hands-free layered layout that handles realistic system sizes.       |
| OMG SysML v2.0 coverage     | 60+ element types and 30+ edge types from the OMG specification.     |
| Workspace + commit          | Edit, snapshot, reset to canonical, or commit to substrate + AIRGen. |
| HTTP API (`/api/v1`)        | Read-only, Bearer-token auth, never returns bare arrays.             |
| MCP server                  | Same surface, exposed at `/mcp` and as a CLI (`@derive-ltd/reify`).  |
| Audit trail                 | `WORKSPACE_COMMIT` history per project, newest first.                |
| GitHub OAuth                | Sign in with GitHub; tokens hashed at rest.                          |

## Views

Each project lives at `/p/<slug>/`. The available views are:

| Route       | Tab label              | Type                                       |
| ----------- | ---------------------- | ------------------------------------------ |
| `/`         | **CXD** Context        | structural (system boundary)               |
| `/bdd`      | **BDD** Block Definition | structural                               |
| `/ibd`      | **IBD** Internal Block | structural (instances + connections)       |
| `/req`      | **REQ** Requirements   | per-requirement traceability subgraph      |
| `/tbl`      | **TBL** Tables         | requirements / blocks as tables            |
| `/stm`      | **STM** State Machine  | behavioural                                |
| `/saf`      | **SAF** Safety         | per-hazard analysis subgraph               |
| `/uc`       | **UC** Use Cases       | behavioural                                |
| `/act`      | **ACT** Actions        | behavioural                                |
| `/ffd`      | **PFD** Phys. Flow     | behavioural (physical flow)                |
| `/seq`      | **SD** Sequence        | behavioural                                |
| `/sysml`    | **{ }** SysML Text     | multi-file CodeMirror editor               |

Twelve view tabs in the project nav rail. The root URL renders the
**CXD** Context Diagram. The REQ and SAF views are
**element-scoped**: the canvas shows one requirement (or one
hazard) at a time, picked from a sidebar. Every view also has an
"Open in Derive →" link that takes you to that project's Derive
workspace.

The view system is data-driven: each project page reads its SysML v2
source, parses it once, caches the AST in IndexedDB, and renders any of
the views above on demand.

## Programmatic access

### HTTP API

Routes live under `/api/v1/*`. Two auth methods, checked in this order:

1. **Bearer token** — `Authorization: Bearer rfy_...`
2. **Cookie session** — the same session cookie the UI uses

All v1 tokens carry the `read` scope; writes are not exposed in v1.

```sh
# Project list
curl -H "Authorization: Bearer rfy_..." \
     https://reify.airgen.studio/api/v1/projects

# A BDD diagram, scoped to a subsystem
curl -H "Authorization: Bearer rfy_..." \
     "https://reify.airgen.studio/api/v1/projects/<slug>/diagrams/bdd?scope=Power%20Subsystem"

# Dry-run a SysML edit (parses, diffs against substrate + AIRGen,
# runs pre-commit checks — applies nothing)
curl -X POST -H "Authorization: Bearer rfy_..." \
     -H "Content-Type: application/json" \
     -d '{"sysml": "package X { part def P { } }"}' \
     https://reify.airgen.studio/api/v1/projects/<slug>/workspace/dry-run
```

The full surface covers projects, SysML (canonical and workspace), views,
diagrams (`bdd`, `ibd`, `act`, `saf`, `req`, `stm`, `cxd`, `uc`, `ffd`),
requirements, trace links, raw substrate facts, workspace state, dry-run,
and audit history.

> Note: dry-run is exposed via the API but not as a button in the
> editor UI. The UI flow is edit → Commit (with optional
> `Snapshot version` to name a save-point and `Reset all to
> generated` to discard). To dry-run before committing, hit the
> API directly.

### MCP server / CLI

The same data is exposed via MCP at `/mcp`. The `@derive-ltd/reify`
package ships an MCP server (stdio) and a `reify` CLI:

```sh
export REIFY_API_URL=https://reify.airgen.studio
export REIFY_API_TOKEN=rfy_...

npx @derive-ltd/reify reify list-projects
npx @derive-ltd/reify reify get-diagram <slug> --type=bdd --scope="Power Subsystem"
npx @derive-ltd/reify reify list-tools         # introspect available tools
```

CLI command names mirror MCP tool names — new server-side tools appear
automatically with no client changes.

## Guides

Start here:

- [Getting started](./getting-started.md) — first-run walkthrough

In-depth guides:

- [Reading a Requirements (REQ) diagram](./guides/reading-a-requirements-req-diagram.md)
- [Reading a Safety (SAF) diagram](./guides/reading-a-safety-saf-diagram.md)
- [Editing SysML v2 source: workspace, snapshot, and commit](./guides/editing-sysml-v2-source-workspace-dry-run-and-commit.md)
- [Using the Reify HTTP API](./guides/using-the-reify-http-api.md)
- [Using the Reify MCP server from Claude Desktop](./guides/using-the-reify-mcp-server-from-claude-desktop.md)
- [Embedding `sysml-reactflow` in your own application](./guides/embedding-sysml-reactflow-in-your-own-application.md)
