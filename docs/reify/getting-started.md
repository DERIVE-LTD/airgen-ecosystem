# Getting started with Reify

Reify visualises the structured systems-engineering data produced by
[Derive](../derive/) and held in [AIRGen](../airgen/). This guide walks
through opening a project, reading its diagrams, and editing the SysML
v2 source.

> **Prerequisites:**
>
> - Access to [reify.airgen.studio](https://reify.airgen.studio) (or a
>   self-hosted Reify instance)
> - A GitHub account for OAuth sign-in
> - A project that already has SysML v2 source — typically produced by
>   the AIRGen / Derive pipeline

## 1. Sign in

Sign in via GitHub. Your GitHub identity maps to a Reify account; an
operator must associate it with the projects you should see.

_TODO: screenshot of the sign-in screen._

## 2. Open a project

The landing page lists the projects you have access to. Each project
has its own slug and lives at `/p/<slug>/`.

The project loads its SysML v2 source once, parses it client-side, and
caches the AST in IndexedDB so subsequent view switches are instant.

## 3. Pick a starting view

Reify offers fourteen views per project. Good starting points:

- **`/cxd` Context Diagram** — see the system in its environment.
- **`/req` Requirements diagram** — see requirement containment and
  `satisfy` / `derive` links.
- **`/saf` Safety analysis** — see hazards alongside their mitigating
  requirements.
- **`/tbl` Tabular view** — view requirements or blocks as tables when
  diagrams are not the right tool.

## 4. Edit the SysML source

Open `/sysml`. The CodeMirror editor shows the project's SysML v2
source. Edit it and switch to any diagram view — the diagram re-renders
from your edits.

_TODO: screenshot of the editor + diagram side-by-side._

## 5. Try the API

Reify exposes a Bearer-token-authenticated read-only HTTP API:

```sh
curl -H "Authorization: Bearer rfy_..." \
     https://reify.airgen.studio/api/v1/projects/<slug>
```

The same data is also available over MCP — install the
`@derive-ltd/reify` CLI and call any tool by name:

```sh
npx @derive-ltd/reify reify list-projects
npx @derive-ltd/reify reify get-diagram <slug> --type=bdd
```

To generate an API token, see the Reify project README.

## Next steps

- _TODO: link to "Reading a Requirements (REQ) diagram" guide_
- _TODO: link to "Editing SysML v2 source" guide_
- _TODO: link to "Using the Reify MCP server from Claude Desktop"_
