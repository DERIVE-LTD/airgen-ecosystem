# Getting started with Reify

Reify visualises the structured systems-engineering data produced by
[Derive](../derive/) and held in [AIRGen](../airgen/). This guide walks
through opening a project, reading its diagrams, and editing the SysML
v2 source safely with the workspace + dry-run flow.

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

Reify maintains two distinct sources for each project:

- **Canonical** — reconstructed from the substrate knowledge graph and
  AIRGen requirements. This is the truth.
- **Workspace** — your in-progress edits. Workspaces are private to
  your session until committed.

When the workspace differs from canonical, the project shows a **drift**
indicator. You can dry-run, commit, or discard at any time.

_TODO: screenshot of the editor showing the drift indicator._

## 5. Dry-run before committing

Before any change becomes part of the canonical model, run a **dry-run**.
A dry-run parses your SysML, diffs it against the substrate and AIRGen
state, and runs pre-commit checks — but applies nothing.

This is the recommended way to:

- Catch parser errors early
- Preview which substrate facts and AIRGen requirements your edit
  would create, update, or delete
- Validate that no existing trace links would dangle

## 6. Try the API

Reify exposes the same data through a Bearer-token-authenticated HTTP API.

```sh
curl -H "Authorization: Bearer rfy_..." \
     https://reify.airgen.studio/api/v1/projects/<slug>
```

Or, more conveniently, use the `@derive-ltd/reify` CLI:

```sh
export REIFY_API_URL=https://reify.airgen.studio
export REIFY_API_TOKEN=rfy_...

npx @derive-ltd/reify reify list-projects
npx @derive-ltd/reify reify get-diagram <slug> --type=bdd
```

To generate an API token, ask the operator of your Reify instance to
run `app/scripts/generate-api-token.ts`. The plaintext is shown once;
only a sha256 hash is stored.

## 7. Connect Claude Desktop (optional)

The same MCP surface plugs into Claude Desktop. Add the following to
`claude_desktop_config.json`:

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

Restart Claude Desktop and the Reify tools appear in the tool picker.

## Next steps

- _TODO: link to "Reading a Requirements (REQ) diagram" guide_
- _TODO: link to "Editing SysML v2 source: workspace, dry-run, and commit"_
- _TODO: link to "Using the Reify MCP server from Claude Desktop"_
- _TODO: link to "Embedding `sysml-reactflow` in your own application"_
