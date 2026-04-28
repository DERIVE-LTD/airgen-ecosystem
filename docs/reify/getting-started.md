# Getting started with Reify

Reify visualises the structured systems-engineering data produced by
[Derive](../derive/) and held in [AIRGen](../airgen/). This guide walks
through opening a project, reading its diagrams, and editing the SysML
v2 source via Reify's multi-file workspace.

> **Prerequisites:**
>
> - Access to [reify.airgen.studio](https://reify.airgen.studio) (or a
>   self-hosted Reify instance)
> - A GitHub account for OAuth sign-in
> - A project that already has SysML v2 source — typically produced by
>   the AIRGen / Derive pipeline
>
> If you just want to look around without signing in, the public
> read-only demo lives at [`/demo`](https://reify.airgen.studio/demo).

## 1. Sign in

Sign in via GitHub. Your GitHub identity maps to a Reify account; an
operator must associate it with the projects you should see.

## 2. Open a project

The landing page lists the projects you have access to. Each project
has its own slug and lives at `/p/<slug>/`.

The project loads its SysML v2 source once, parses it client-side, and
caches the AST in IndexedDB so subsequent view switches are instant.

The project nav rail has **twelve view tabs** (CXD, BDD, IBD, REQ,
TBL, STM, SAF, UC, ACT, PFD, SD, `{ }` SysML Text), plus an
`Open in Derive →` link that takes you to the corresponding Derive
project workspace.

## 3. Pick a starting view

Reify offers twelve views per project. Good starting points:

- **`/` CXD Context Diagram** — the project's root URL renders the
  context diagram: stakeholders on one side, the system in the
  middle, external interfaces on the other side.
- **`/req` Requirements** — pick a requirement from the sidebar and
  see only its directly-connected upstream/downstream nodes.
- **`/saf` Safety** — pick a hazard from the sidebar and see its
  mitigation chain.
- **`/tbl` Tables** — view requirements or blocks as tables when
  diagrams are not the right tool.

The REQ and SAF views are **element-scoped**: there is no
"all requirements at once" or "all hazards at once" overview.
Pick from the sidebar to focus.

## 4. Edit the SysML source

Open `/sysml`. The editor splits the project's SysML across **eleven
files** in a side picker:

- `Views.sysml`
- `Stakeholders.sysml`
- `Parts.sysml`
- `Connections.sysml`
- `Interfaces.sysml`
- `Actions.sysml`
- `States.sysml`
- `Hazards.sysml`
- `Constraints.sysml`
- `Requirements.sysml`
- `Traceability.sysml`

Each file is a CodeMirror buffer. Edits update your workspace; the
canonical source isn't touched until you commit.

The toolbar above the editor offers four actions:

- **`Snapshot version`** — name and save the current workspace state
  as a snapshot you can come back to.
- **`Reset all to generated`** — discard the workspace and revert
  every file to the canonical (substrate + AIRGen) reconstruction.
- **`Commit`** — apply the workspace edits to the substrate and
  AIRGen.
- **`History`** — open the commit history panel.

A **dry-run** before commit is available via the API
(`POST /api/v1/projects/<slug>/workspace/dry-run`) but isn't
surfaced as a UI button at the time of writing.

## 5. Commit

Commit applies your workspace to the substrate and AIRGen. Each
commit is recorded in the project's audit trail
(`/api/v1/projects/<slug>/audit`) as a `WORKSPACE_COMMIT` entry,
newest first.

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

- [Reading a Requirements (REQ) diagram](./guides/reading-a-requirements-req-diagram.md)
- [Reading a Safety (SAF) diagram](./guides/reading-a-safety-saf-diagram.md)
- [Editing SysML v2 source: workspace, snapshot, and commit](./guides/editing-sysml-v2-source-workspace-dry-run-and-commit.md)
- [Using the Reify HTTP API](./guides/using-the-reify-http-api.md)
- [Using the Reify MCP server from Claude Desktop](./guides/using-the-reify-mcp-server-from-claude-desktop.md)
- [Embedding `sysml-reactflow` in your own application](./guides/embedding-sysml-reactflow-in-your-own-application.md)
