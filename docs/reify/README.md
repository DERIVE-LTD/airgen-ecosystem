# Reify

**SysML v2 model viewer and editor.**

Reify renders structural, behavioural, and requirements diagrams
straight from a project's SysML v2 source. It pairs a CodeMirror
editor for the SysML text with React Flow + ELK auto-layout for the
diagrams, and exposes the same data through both an HTTP API and an
MCP server for programmatic and agentic access.

- **Hosted:** [reify.airgen.studio](https://reify.airgen.studio)
- **Stack:** Next.js 16 (App Router) on React 19, Tailwind 4,
  CodeMirror 6, React Flow 12, ELK.js, Mermaid 11
- **Published packages:**
  - `sysml-reactflow` — open-source React component library for SysML v2
  - `@derive-ltd/reify` — MCP server and CLI for the public API

## What Reify does

| Capability                  | Summary                                                              |
| --------------------------- | -------------------------------------------------------------------- |
| Live SysML v2 editor        | Edit the SysML source in CodeMirror, watch diagrams re-render.       |
| Fourteen views              | One canonical diagram per system aspect, plus tabular and overview.  |
| ELK auto-layout             | Hands-free layered layout that handles realistic system sizes.       |
| OMG SysML v2.0 coverage     | 68 node types and 35 edge types from the OMG specification.          |
| HTTP API                    | Read-only `/api/v1/*` over Bearer-token auth.                        |
| MCP server                  | Same surface, also available as `@derive-ltd/reify` CLI.             |
| GitHub OAuth                | Sign in with GitHub; tokens hashed at rest.                          |

## Views

Each project lives at `/p/<slug>/`. The available views are:

| Route       | View                          | Type                                       |
| ----------- | ----------------------------- | ------------------------------------------ |
| `/`         | Overview / summary            | mixed                                      |
| `/sysml`    | SysML v2 source editor        | CodeMirror text editor                     |
| `/bdd`      | Block Definition Diagram      | structural                                 |
| `/ibd`      | Internal Block Diagram        | structural (instances + connections)       |
| `/cxd`      | Context Diagram               | structural (system boundary)               |
| `/req`      | Requirements diagram          | requirement containment + satisfy/derive   |
| `/uc`       | Use Case diagram              | behavioural                                |
| `/act`      | Activity diagram              | behavioural                                |
| `/seq`      | Sequence diagram              | behavioural                                |
| `/stm`      | State Machine diagram         | behavioural                                |
| `/ffd`      | Function-Flow Diagram         | behavioural                                |
| `/saf`      | Safety analysis view          | hazards / mitigations                      |
| `/tbl`      | Tabular view                  | requirements / blocks as tables            |
| `/sessions` | Agent session history         | per-project Claude runs                    |

The view system is data-driven: each project page reads its SysML v2
source, parses it once, and renders any of the views above on demand.

## Programmatic access

### HTTP

```sh
curl -H "Authorization: Bearer rfy_..." \
     https://reify.airgen.studio/api/v1/projects/<slug>
```

The API view of a project is identical to the UI view — both are
served by the same data-access functions.

### MCP / CLI

The same surface is exposed via MCP. The `@derive-ltd/reify` package
ships an MCP server (`reify-mcp`, stdio transport) and a `reify` CLI
that calls the remote `/mcp` endpoint:

```sh
npx @derive-ltd/reify reify list-projects
npx @derive-ltd/reify reify get-diagram <slug> --type=bdd --scope="Power Subsystem"
```

CLI command names mirror the MCP tool names — new tools appear in
`reify list-tools` with no client-side changes.

## Guides

- [Getting started](./getting-started.md) — first-run walkthrough
- [`guides/`](./guides/) — task-oriented guides _(coming soon)_

Planned topics:

- Reading a Requirements (REQ) diagram
- Reading a Safety (SAF) diagram
- Editing SysML v2 source and watching diagrams update
- Using the Reify HTTP API
- Using the Reify MCP server from Claude Desktop
- Embedding `sysml-reactflow` in your own application
