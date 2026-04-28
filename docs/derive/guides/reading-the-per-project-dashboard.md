# Reading the per-project dashboard and spec-tree

The per-project dashboard at `/p/<slug>/dashboard` is Derive's
single-screen overview of a project. It surfaces the harness
workflow state, headline metrics, harness telemetry, the safety
regime, the spec-tree, and recent sessions. This guide walks through
each section and what to look for in a one-minute health-check.

> **Prerequisites:** access to a Derive instance and a project to
> open. If you've never used Derive, start with
> [the getting-started guide](../getting-started.md).

## Page layout

The dashboard is a single scrollable column. The left rail is
project navigation only — it lists Dashboard, Artifacts, Report,
Log, Control, and Downloads. All the dashboard content lives in the
main pane.

In top-to-bottom order, the dashboard sections are:

1. **Workflow** — visual state machine of the harness pipeline.
2. **Metric tiles** — eight project-wide counters.
3. **HARNESS** — live harness telemetry.
4. **SAFETY REGIME** — regulatory context for the project.
5. **SPEC TREE** — subsystem breakdown with status.
6. **Recent Sessions** — latest journal entries.

## Workflow

The Workflow card visualises the harness state machine for this
project. The phases run:

```
Idle  →  Concept  →  Scaffold  →  Decompose  →  First Pass
      →  QC Reviewed  →  Validated  →  Red Teamed  →  Complete
```

Each row shows:

- A status indicator (filled green dot = completed, half-filled
  amber = current, empty = not yet reached).
- The phase name and the workflow tag (`concept`, `scaffold`,
  `decompose`, `qc`, `validate`, `review`, `red-team`, `current`).
- A one-line description of what that phase produces.

Read this first when opening a project — it tells you exactly where
in the pipeline the autonomous loop currently is.

## Metric tiles

Eight tiles, four-by-two, with the project's headline counters:

| Tile           | What it counts                                          |
| -------------- | ------------------------------------------------------- |
| Requirements   | Total requirements across all documents.                |
| Documents      | Number of documents (system, sub-system, …).            |
| Diagrams       | Architecture diagrams.                                  |
| Traces         | Trace links of all types.                               |
| Entities       | Entities classified in UHT Substrate.                   |
| Facts          | Substrate facts under the project namespace.            |
| Last Flow      | The most recent harness flow that ran (e.g. `tech-author`). |
| Last Session   | How long ago the last session completed.                |

These are absolute counts, not deltas — for trends, look at the
journal or run an `airgen diff` against a previous baseline.

## HARNESS

A small status strip with four fields:

- **Timer** — `running` or `paused`. The harness scheduler state.
- **Sessions** — total sessions ever run for this project.
- **Last Cost** — Claude API cost of the most recent session.
- **Directive** — the current standing directive (e.g. `PAUSE`,
  `RESUME`, or a directive name).

Use this for cost tracking and to confirm at a glance that the
loop is doing what you expect.

## SAFETY REGIME

The safety regime card shows the regulatory framework the project
is being evaluated against. The card has a status badge in the
corner (e.g. `NONE`, `SET`, `CONFIRMED`).

Fields:

- **Primary** — the primary regime (e.g. SIL-2, ASIL-D, DAL-A,
  tool-qualification, or "not set").
- **Security** — security regime if applicable (e.g. ISO 27001).
- **Tool-qualification** — for tools producing safety-critical
  output (e.g. DO-330).
- **Confirmed** — yes/no, whether the regime has been
  human-confirmed.
- **Hazards w/ regime** — count of hazards that have a regime
  assignment, over total hazards (e.g. `0 / 7`).
- **Reqs w/ level tag** — count of requirements that carry a
  SIL / DAL / ASIL level tag.

If the regime hasn't been set, the card shows guidance like:

> *"No PRIMARY_SAFETY_REGIME fact — run the concept flow's
> regime-selection step."*

Follow that guidance to set the regime via a harness session.

## SPEC TREE

The bottom of the dashboard shows the project's spec-tree as a
table with three columns:

| SUBSYSTEM            | LEVEL | STATUS    |
| -------------------- | ----- | --------- |
| Vehicle Platform     | 2     | Complete  |
| Communication Subsystem | 1  | Complete  |
| …                    | …     | …         |

A "Full report" link in the top-right of the spec-tree section
takes you to the project's full Report view (`/p/<slug>/report`),
which renders the spec-tree in much more detail along with all the
ConOps, Requirements, Design, Verification, and Hazards content.

## Recent Sessions

The bottom section is a feed of the most recent harness sessions
for this project — clickable through to individual journal
entries. Each row shows:

- Session timestamp.
- Workflow phase or flow name.
- Outcome (ok / partial / failed).

Click any row to read the full markdown post in the Log view.

## A one-minute health check

Top-to-bottom scan:

1. **Workflow.** Where in the pipeline is the project? Anything
   stuck at a phase for unusually long?
2. **Metric tiles.** Numbers reasonable? Big jumps since you last
   looked?
3. **HARNESS.** Timer running? Last cost normal? What's the current
   directive?
4. **Safety Regime.** Set or not set? Confirmed? Coverage of hazards
   and requirements?
5. **Spec Tree.** Any subsystem in an unexpected status?
6. **Recent Sessions.** Anything failed or unexpected?

Sixty seconds. If anything looks off, open the relevant tab —
Control for loop control, Log for narrative detail, Quality Gates
(direct URL `/p/<slug>/quality`) for harness gate state.

## The dashboard from the CLI

```sh
# Stats overview
airgen report stats <tenant> <slug>

# Compliance summary
airgen report compliance <tenant> <slug>

# Recent activity
airgen activity list <tenant> <slug> --limit 20
```

Useful when you want to script a multi-project status report.

## What's next

- [The quality view](./the-quality-view.md)
- [Driving the autonomous loop](./driving-the-autonomous-loop.md)
- [Working with baselines and artefact bundles](./working-with-baselines-and-artefact-bundles.md)
