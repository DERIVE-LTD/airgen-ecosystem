# Editing SysML v2 source: workspace, snapshot, and commit

Reify's SysML editor sits on top of a workspace model. The
canonical source — what every diagram view renders from when no
edits are pending — is reconstructed from UHT Substrate facts and
AIRGen requirements. Your edits go into a workspace that is
yours alone until you commit it back to the substrate and AIRGen.

This guide walks the actual editor UI, the API-only dry-run,
and the audit trail.

> **Prerequisites:** a Reify project and a login that has write
> access to it. The public demo at `/demo` lets you open the
> editor read-only; commits require an authenticated user with
> write permission.

## The two SysML sources

Each project has two distinct SysML v2 sources:

- **Canonical** — reconstructed from UHT Substrate facts and AIRGen
  requirements. This is the truth: every render in any other Reify
  view derives from canonical source.
- **Workspace** — your in-progress edits. Workspaces are private to
  your session until committed.

Workspace state is exposed at `/api/v1/projects/<slug>/workspace`
which returns `{ exists, meta, ... }`. You can poll this endpoint
to see whether a workspace is pending and how it differs from
canonical.

## 1. Open the editor

Navigate to `/p/<slug>/sysml`. The editor splits the project's
SysML across **eleven files** in a left-side file picker:

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

Each file is a CodeMirror buffer with line numbers, syntax
highlighting, and section folding. The current file's line count
is shown above the editor.

A right-side panel lists the section anchors (named declarations
in the current file) so you can jump to a specific definition.

## 2. The toolbar

Four actions live in the toolbar above the editor:

| Button                   | Effect                                                                |
| ------------------------ | --------------------------------------------------------------------- |
| **Snapshot version**     | Save the current workspace state as a named snapshot you can revisit. |
| **Reset all to generated** | Discard the workspace; every file reverts to canonical.            |
| **Commit**               | Apply the workspace edits to substrate + AIRGen in one step.          |
| **History**              | Open the commit-history panel for this project.                       |

There is **no separate dry-run button in the UI**. Dry-run is
exposed via the API (see below); operators who want to preview a
commit before applying it should call the API or rely on the
commit operation's pre-commit checks.

The header also has `Download .sysml` (export the workspace
content) and `View in Derive →` (jump to the Derive project page).

## 3. Make an edit

Type into any of the eleven file buffers. Edits go to the
workspace, not to canonical. Switch to any diagram view
(`/p/<slug>/bdd`, `/req`, `/saf`, …) and the diagrams re-render
from your workspace if it differs from canonical, otherwise from
canonical directly.

Switching files within the editor is instant — buffers are
held in memory.

## 4. Snapshot before risky changes

Before a substantial restructuring, click **Snapshot version**
and give the snapshot a name. If you decide the change isn't
working, you can return to the named snapshot rather than to
canonical.

Snapshots are workspace-local — they don't show up in the
audit trail until you commit one.

## 5. Dry-run via the API

A dry-run parses workspace SysML, diffs it against the substrate
and AIRGen state, and runs pre-commit checks — but applies
nothing. Use this in CI or in an external script before clicking
Commit.

```sh
curl -X POST -H "Authorization: Bearer rfy_..." \
     -H "Content-Type: application/json" \
     -d "{\"sysml\": \"$(cat workspace.sysml)\"}" \
     https://reify.airgen.studio/api/v1/projects/<slug>/workspace/dry-run
```

If `sysml` is omitted from the dry-run body, the API uses the
current workspace buffer.

The response includes:

- **`adds`** — what would be created (entities, facts, requirements).
- **`updates`** — what would change.
- **`deletes`** — what would be removed.
- **`pre_commit_findings`** — orphans, broken trace links, etc.
- **`parse_errors`** — any SysML parser errors that would block commit.

A non-empty `parse_errors` or `pre_commit_findings` means the
commit would fail or surface warnings. In CI, fail the pipeline
on any non-empty array.

## 6. Commit

Click **Commit**. The workspace edits are applied:

- Substrate facts are upserted.
- AIRGen requirements get created/updated/deleted to match.
- The workspace's content becomes the new canonical source.

Each commit appears in the project's audit trail at
`/api/v1/projects/<slug>/audit` as a `WORKSPACE_COMMIT` entry,
newest first. Each entry includes the timestamp, the SysML hash,
and counts of substrate / requirements / links touched.

> The commit endpoint is **not** exposed via the v1 read-only HTTP
> API or the MCP server — it lives behind the cookie-authenticated
> UI. This is intentional: writes go through the UI's auth path, not
> through Bearer tokens.

## 7. Discard

If you decide your workspace edits weren't right, click
**Reset all to generated**. Every file reverts to canonical;
unsaved edits are lost. Snapshots survive this — they're kept
separately.

There is no auto-reset. A workspace persists across sessions
until you commit, snapshot, or reset it explicitly.

## 8. Read the commit history

```sh
curl -H "Authorization: Bearer rfy_..." \
     https://reify.airgen.studio/api/v1/projects/<slug>/audit
```

Returns `{ items: [...] }` — the chain of `WORKSPACE_COMMIT`
entries, newest first. Each entry includes:

- `timestamp` (ISO 8601).
- `raw` — a pipe-delimited summary line containing the SysML
  hash and the substrate / requirements / links counts.
- Author and commit-message metadata where present.

The same history is reachable from the editor's `History` button.

## Common patterns

### Tight-loop edit-and-render

Open the editor in one window and a diagram view (e.g. `/bdd`,
`/req`) in another. Edit a file in the workspace; the diagram
re-renders. Iterate until the diagram looks right, then commit.

### Big-bang reorg

For substantial restructuring across multiple files, snapshot
first, then edit. If the result is right, commit. If not, reset.

### Dry-run-in-CI

Wire your CI to call the dry-run endpoint with the SysML you
intend to commit and fail the pipeline on any `parse_errors` or
`pre_commit_findings`. The Reify UI doesn't expose this gate;
your CI does.

### Concurrent editors

Reify's workspace is per-user, so two engineers editing in
parallel don't see each other's drafts. They can both **commit**
to the same canonical source. The audit trail records who did
what; conflict resolution is conventional source-control style —
last-commit-wins. For projects with many concurrent editors,
schedule edits or split into smaller scoped subsystems.

## What's next

- [Reading a Requirements (REQ) diagram](./reading-a-requirements-req-diagram.md)
- [Using the Reify HTTP API](./using-the-reify-http-api.md)
- [Embedding `sysml-reactflow` in your own application](./embedding-sysml-reactflow-in-your-own-application.md)
