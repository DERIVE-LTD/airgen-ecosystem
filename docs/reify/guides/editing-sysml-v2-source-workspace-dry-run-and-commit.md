# Editing SysML v2 source: workspace, dry-run, and commit

Reify's SysML editor isn't just a text editor — it sits on top of a
workspace model that lets you stage edits, dry-run them against the
substrate and AIRGen, and commit only when you're satisfied. This
guide explains the workspace lifecycle and the tools at each stage.

> **Prerequisites:** a Reify project with existing SysML v2 source
> and a Bearer token / login that has write access (workspaces are
> per-user).

## The two SysML sources

Each project has two distinct SysML v2 sources:

- **Canonical** — reconstructed from UHT Substrate facts and AIRGen
  requirements. This is the truth: every render in any other Reify
  view derives from canonical source.
- **Workspace** — your in-progress edits. Workspaces are private to
  your session until committed.

When the workspace differs from canonical, the project shows a
**drift indicator**. You can dry-run, commit, or discard at any time.

## 1. Open the editor

Navigate to `/p/<slug>/sysml`. The CodeMirror editor loads the
project's canonical SysML v2 source. Edits here go into your
workspace, not into the canonical source.

The editor has:

- Syntax highlighting for SysML v2.
- Section folding by package / part definition.
- Line numbers and column tracking.
- A status bar showing the workspace state (clean, drifted, parsing,
  with errors).

## 2. Make an edit

Type into the editor. Each keystroke updates your workspace — there
is no separate "save" step. The status bar transitions from
**clean** to **drifted** as soon as you type.

Switch to any diagram view (`/p/<slug>/bdd`, `/req`, `/saf`, …) and
the diagrams re-render from your workspace, not from canonical.
This is the fastest feedback loop available — see the impact of an
edit instantly across all views.

## 3. Dry-run before committing

Once your edits look good, **dry-run** before committing. A dry-run:

1. Parses your workspace SysML.
2. Diffs the parsed result against the substrate and AIRGen state.
3. Runs pre-commit checks — orphan detection, schema validation,
   trace link consistency.
4. Returns a structured report.

Critically, **a dry-run applies nothing**. It tells you exactly what
the commit will do without doing it.

In the UI, the dry-run button is in the editor toolbar; the report
opens in a side panel.

Via the API:

```sh
curl -X POST -H "Authorization: Bearer rfy_..." \
     -H "Content-Type: application/json" \
     -d "{\"sysml\": \"$(cat workspace.sysml)\"}" \
     https://reify.airgen.studio/api/v1/projects/<slug>/workspace/dry-run
```

The response includes:

- **`adds`** — what would be created (entities, facts, requirements).
- **`updates`** — what would change.
- **`deletes`** — what would be removed.
- **`pre_commit_findings`** — orphans, broken trace links, etc.
- **`parse_errors`** — any SysML parser errors that would block commit.

## 4. Read the dry-run report

A typical dry-run for a moderate edit:

```
Dry-run: project widget-x
─────────────────────────

Adds (3)
  · entity "Thermal Radiator" in SE:widget-x
  · fact "Thermal Radiator" PART_OF "Thermal Loop"
  · requirement SYS-027 "The radiator shall dissipate ≥ 250 W ..."

Updates (1)
  · requirement SYS-014 — text changed

Deletes (0)

Pre-commit findings: clean

Parse errors: none
```

The dry-run is your last chance to catch:

- **Accidentally deleted requirements** (a typo in the SysML can
  remove a definition, which the dry-run translates into a delete).
- **Broken trace links** — if you renamed a requirement reference,
  any trace link still pointing at the old reference shows up here.
- **Orphan facts** — substrate facts that would no longer have a
  corresponding entity.

If anything looks wrong, edit and dry-run again.

## 5. Commit

Commit applies the dry-run plan: substrate facts get upserted,
AIRGen requirements get created/updated/deleted, and the workspace
syncs to canonical.

The UI commit button asks for a one-line message ("commit message")
that goes into the audit trail. Pick something readable — it will
show up in `WORKSPACE_COMMIT` audit entries forever.

After commit, the status bar returns to **clean**. The workspace and
canonical are now identical.

> The commit endpoint is **not** exposed via the v1 read-only HTTP
> API or the MCP server — it lives behind the cookie-authenticated
> UI. This is intentional: writes go through the UI's auth path, not
> through Bearer tokens.

## 6. Discard the workspace

If you decide your edits weren't right, discard:

- **UI** — Discard button in the editor toolbar (with confirmation).
- **Effect** — workspace resets to canonical; any unsaved edits
  are lost.

There is no auto-discard. A workspace persists across sessions until
you commit or discard it explicitly. This is a feature: you can
start an edit on Friday, log off, and pick it up on Monday.

## 7. Inspect commit history

```sh
curl -H "Authorization: Bearer rfy_..." \
     https://reify.airgen.studio/api/v1/projects/<slug>/audit
```

Returns the chain of `WORKSPACE_COMMIT` entries, newest first. Each
entry includes:

- Timestamp.
- Author.
- SysML hash before / after.
- Counts of substrate facts, requirements, and links touched.
- Commit message.

This is the project's source-of-truth audit log for SysML edits.

## Common patterns

### Tight-loop edit-and-render

Open the editor in one window and a diagram view in another. Edit;
the diagram re-renders from your workspace. Iterate until the
diagram looks right, then dry-run.

### Big-bang reorg

For substantial restructuring, do the edit in your local editor (any
text editor with SysML highlighting), then paste into the workspace
in one go. Dry-run *immediately* — the parser will catch any
syntax issue before you've spent time looking at diagrams.

### Concurrent editors

Reify's workspace is per-user, so two engineers editing in parallel
don't see each other's drafts. But they can both **commit** to the
same canonical source. The audit trail records who did what; conflict
resolution is conventional source-control style — last-commit-wins.
For projects with many concurrent editors, schedule edits or split
the project into smaller scoped subsystems.

## What's next

- [Reading a Requirements (REQ) diagram](./reading-a-requirements-req-diagram.md)
- [Using the Reify HTTP API](./using-the-reify-http-api.md)
- [Embedding `sysml-reactflow` in your own application](./embedding-sysml-reactflow-in-your-own-application.md)
