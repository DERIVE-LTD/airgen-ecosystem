# Reading a Requirements (REQ) diagram

The REQ diagram is Reify's view of a project's requirements topology
— who derives from whom, what satisfies what, and what verifies what.
This guide walks through the layout, the symbology, and a few
practical reading patterns.

> **Prerequisites:** access to a Reify project with at least some
> requirements and trace links. See
> [Getting started with Reify](../getting-started.md) if you don't
> have one.

## The four-column layout

Open `/p/<slug>/req`. The diagram is laid out in four columns left
to right, mirroring the natural flow of requirements engineering:

```
┌────────────┐  ┌────────────┐  ┌──────────────┐  ┌────────────────┐
│ STAKEHOLDER│  │   SYSTEM   │  │ SUB-SYSTEM / │  │ VERIFICATION   │
│  needs     │  │ requirements│ │ INTERFACE    │  │  activities    │
└────────────┘  └────────────┘  └──────────────┘  └────────────────┘
```

Column 1 — stakeholder needs — sits leftmost; verification activities
sit rightmost. Each requirement is a node; each trace link is an
edge. The flow reads left → right: stakeholder → system → sub-system
or interface → verification.

This isn't a strict topological constraint. A stakeholder need can
have a verification activity directly attached if the link exists in
AIRGen. But the columnar layout reflects the *typical* flow and
makes anomalies visually obvious — a stakeholder need with no
descendants stands out as a column-1 node with no outgoing edges.

## Node symbols

Each requirement node carries:

- **A header band** with the requirement's reference (e.g. `STK-007`,
  `SYS-014`).
- **The requirement text** (truncated; click to expand).
- **A status colour** indicating QA score range, EARS pattern, or
  lifecycle state (depending on which colour layer you've toggled).
- **A small icon strip** for tags — safety, derived, frozen, etc.

Verification nodes look slightly different — boxed with a method
abbreviation (T / A / I / D for Test, Analysis, Inspection,
Demonstration) and a status pill (passed, failed, in progress,
blocked).

## Edge types

Edges are coloured and labelled by type:

| Type        | Visual                          | Reading                                        |
| ----------- | ------------------------------- | ---------------------------------------------- |
| `derives`   | solid arrow, `derives` label    | child derives from parent.                     |
| `satisfies` | solid arrow, `satisfies` label  | child satisfies a higher-level requirement.    |
| `refines`   | dashed arrow                    | child refines parent.                          |
| `verifies`  | dashed arrow into a verif node  | activity verifies requirement.                 |
| `implements`| double-stroke arrow             | implementation fulfils requirement.            |
| `conflicts` | red, two-headed arrow           | the two requirements cannot both hold.         |

Hover an edge to see the full link metadata (link ID, linkset
membership, who created it).

## Reading patterns

### 1. Find untraced requirements quickly

Look for nodes in column 2 (system) or column 3 (sub-system) with
**no incoming edge**. Those are requirements that derive from
nothing — usually a missed trace, occasionally a deliberately
isolated requirement.

The same shortcut applies in column 4: a requirement with no
verification activity is column-3 with no edge crossing into
column 4.

### 2. Follow a chain end-to-end

Pick a stakeholder need and click it. Reify highlights the entire
descendant subgraph — every system requirement derived from it,
every sub-system requirement under those, and the verification
activities at the leaves. It's the fastest way to answer "is this
need fully covered?"

### 3. Spot conflicts

Conflicts are red and two-headed. They should be rare; when they
exist, click for the rationale and resolve them by:

- Refining one of the requirements until they no longer conflict
- Deleting one of them (with a justification note)
- Documenting that the conflict is intentional (a trade-off being
  carried into review)

### 4. Audit verification coverage

Filter the diagram to show only the trace edges of type `verifies`.
You should see a near-complete bipartite graph between column 3 and
column 4 — every requirement on the left should have at least one
incoming verification edge from the right. Holes are unverified
requirements.

## Per-element tabs

Most diagram types in Reify support per-element tabs (one tab per
subsystem, hazard, etc.). REQ uses **per-document** tabs — pick a
document (`system-requirements`, `interface-requirements`, …) to
scope the diagram to that document's requirements only.

This keeps the diagram readable on projects with hundreds of
requirements: you'd never want to see every node at once.

## Switching layouts

The layout selector (top-right) offers:

- **Layered (default).** ELK's hierarchical layered layout —
  optimised for the 4-column REQ flow.
- **Force-directed.** Better for small diagrams where you want to
  see relationships rather than hierarchy.
- **Tree.** A single root with descendants — useful for printing.

For REQ, layered is almost always right. The other layouts are
there for unusual cases (e.g. visualising a tightly-coupled cluster
of requirements with many cross-links).

## What's next

- [Reading a Safety (SAF) diagram](./reading-a-safety-saf-diagram.md)
- [Editing SysML v2 source: workspace, dry-run, and commit](./editing-sysml-v2-source-workspace-dry-run-and-commit.md)
- [Building a traceability matrix (AIRGen)](../../airgen/guides/building-a-traceability-matrix.md)
