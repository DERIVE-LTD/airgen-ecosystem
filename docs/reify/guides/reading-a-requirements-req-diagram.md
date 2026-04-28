# Reading a Requirements (REQ) diagram

The REQ view at `/p/<slug>/req` is Reify's per-requirement
traceability inspector. Pick a requirement from the sidebar and
the canvas renders only that requirement plus its
directly-connected upstream and downstream nodes — the
requirements it derives from, the requirements that derive from
it, and the verification activities that test it.

This guide walks the layout, the symbology, and the reading
patterns this view is good for.

> **Prerequisites:** access to a Reify project with at least some
> requirements and trace links. See
> [Getting started with Reify](../getting-started.md) if you don't
> have one.

## Layout

The REQ view is two panes:

- **Left sidebar** — every requirement in the project, listed by
  reference (e.g. `SYS-REQ-001`, `SYS-REQ-002`). Above the list
  is a **`+ Create View`** affordance for saving custom view
  definitions.
- **Canvas** — the requirement's local subgraph. Picking a
  requirement updates the URL to
  `/p/<slug>/req?view=view-req-<ref>` and renders that
  requirement at the centre of the canvas.

Above the canvas is a stats strip ("N requirements, N trace
links") and the section header **Requirements Traceability**.
The canvas controls (`Reset layout`, `Minimap`, zoom, fit view)
sit in the corner, along with `Edit SysML` and `Commit` buttons
that take you to the editor flow.

## Why per-requirement, not all-at-once

A project with 200 requirements and 500 trace links is unreadable
as a single graph. The per-requirement view solves the readability
problem at the cost of needing one click to swap focus.

In practice this works because the question you're usually asking
about a requirement is local — *what derives from this? what
verifies this? is its parent reasonable?* — and not global. For
global views, use the AIRGen-side traceability matrix or the
Reify HTTP API to fetch the full graph and render it externally.

## Node types

Each node carries a header band with its kind, e.g.
`<<REQUIREMENT-DEFINITION>>` or `<<REQUIREMENT-USAGE>>`, plus a
type label (`Definition`, `Usage`).

The body shows:

- **The requirement's reference** (e.g. `SYS-REQ-023`).
- **The requirement text** (truncated; expand with the chevron).
- A **▼** caret to expand additional metadata.

Verification nodes look slightly different — they're styled as
verification cases and labelled accordingly.

## Edge types

Edges I've observed in the demo project:

| Type        | Reading                                           |
| ----------- | ------------------------------------------------- |
| `derives`   | Child derives from parent.                        |
| `verifies`  | Verification activity verifies a requirement.     |

The underlying SysML v2 model also supports `satisfies`,
`refines`, `implements`, and conflict relations; these may
appear when the project's source uses them.

Hovering an edge surfaces the link metadata.

## Reading patterns

### 1. Walk a chain

Pick a high-level requirement (e.g. a stakeholder need or a
top-level system requirement). The canvas shows what derives from
it directly. Click any neighbour in the canvas — or pick the
neighbour from the sidebar — and the focus swaps. Repeat to walk
the chain end-to-end.

### 2. Find untraced requirements

Pick a requirement and look at its `derives` edges. A requirement
with no inbound `derives` edge from a higher-level requirement is
an orphan from a derivation standpoint — usually a missed trace.

### 3. Audit verification coverage

Pick a requirement and look at outbound `verifies` edges. No
edges = no verification activity claimed for this requirement.
On a regulated project that's a coverage gap.

To audit coverage globally, walk the sidebar list and check each
requirement's `verifies` edges. The HTTP API is faster for this
— `/api/v1/projects/<slug>/trace-links?type=verifies` returns
every verification edge in one call.

## Saved views

The **`+ Create View`** affordance at the top of the sidebar lets
you save the current focus as a named view. Saved views appear in
the sidebar alongside the per-requirement entries and are
shareable via URL.

## Switching scope

The view is currently locked to per-requirement scope. There's
no layout selector in the canvas (the underlying React Flow
canvas uses the layered ELK layout for traceability data, which
fits the typical "1-to-many derivation" shape).

To see a requirement at full text, click the `▼` caret on its
node, or open `/p/<slug>/sysml` and jump to `Requirements.sysml`.

## Cross-checking with AIRGen

Reify's REQ view renders trace links sourced from AIRGen. To
audit the source data:

```sh
curl -H "Authorization: Bearer rfy_..." \
     "https://reify.airgen.studio/api/v1/projects/<slug>/trace-links?ref=SYS-REQ-001"
```

Returns the trace-link records that touch `SYS-REQ-001`. Compare
with what the canvas renders; mismatch usually means the
canonical SysML is stale (re-render the AST or commit the latest
workspace).

## What's next

- [Reading a Safety (SAF) diagram](./reading-a-safety-saf-diagram.md)
- [Editing SysML v2 source: workspace, snapshot, and commit](./editing-sysml-v2-source-workspace-dry-run-and-commit.md)
- [Building a traceability matrix (AIRGen)](../../airgen/guides/building-a-traceability-matrix.md)
