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

The dashboard is a single scrollable column titled `Dashboard`. The
left rail is the project chrome: a `← Projects` back link, the
project title, a favourite-star toggle, a `Search ⌘K` palette
button, the six project tabs (Dashboard, Artifacts, Report, Log,
Control, Downloads), and the signed-in user with a sign-out icon
pinned to the bottom. A theme toggle (`◐ System`) sits in the
bottom-right of the viewport. All dashboard content lives in the
main pane.

Section headings render in ALL CAPS via CSS; this guide reproduces
the UI casing so readers can find each section by sight.

In top-to-bottom order, the dashboard sections are:

1. **WORKFLOW** — visual state machine of the harness pipeline.
2. **Metric tiles** — eight project-wide counters (no section header).
3. **HARNESS** — live harness telemetry.
4. **SAFETY REGIME** — regulatory context for the project.
5. **SPEC TREE** — subsystem breakdown with status.
6. **RECENT SESSIONS** — latest journal entries.

## WORKFLOW

The Workflow card visualises the harness state machine for this
project as a vertical list of phase rows, in this order:

- Idle
- Concept
- Scaffold
- Decompose
- First Pass
- QC Reviewed
- Validated
- Red Teamed
- Complete

Each row shows:

- A **status indicator**:
  - filled green dot + ✓ — phase complete
  - half-filled amber dot — phase active (current)
  - empty dot — phase not yet reached
- The **phase name**.
- A **flow tag** — the harness flow that runs *from* this phase to
  produce the next phase's deliverables. The tag vocabulary is
  `concept`, `scaffold`, `decompose`, `qc`, `validate`, `review`,
  `red-team`, and `current`. The `review` tag appears on two rows
  (QC Reviewed → Validated and Red Teamed → Complete) because the
  same review flow gates both transitions; `current` is the
  sentinel attached to whichever phase is active.
- A **one-line description** of what that flow produces.

Read this first when opening a project — it tells you exactly where
in the pipeline the autonomous loop currently is.

## Metric tiles

Eight tiles, four-by-two, with the project's headline counters. The
labels are rendered in small caps:

| Tile          | What it counts                                              |
| ------------- | ----------------------------------------------------------- |
| REQUIREMENTS  | Total requirements across all documents.                    |
| DOCUMENTS     | Number of documents (system, sub-system, …).                |
| DIAGRAMS      | Architecture diagrams.                                      |
| TRACES        | Trace links of all types.                                   |
| ENTITIES      | Entities classified in UHT Substrate.                       |
| FACTS         | Substrate facts under the project namespace.                |
| LAST FLOW     | The most recent harness flow that ran (e.g. `tech-author`). |
| LAST SESSION  | How long ago the last session completed.                    |

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
loop is doing what you expect. If `Timer` and `Directive` appear
to disagree (e.g. `Timer running` with `Directive PAUSE`), the
directive applies on the next scheduler tick — they will reconcile.

## SAFETY REGIME

The safety regime card has a status badge in the top-right corner
(`NONE`, `SET`, or `CONFIRMED`) and a six-field grid: three fields
on the top row, three bordered sub-tiles on the bottom row.

Top row:

- **Primary** — the primary regime (e.g. SIL-2, ASIL-D, DAL-A,
  tool-qualification, or `— not set —`).
- **Security** — security regime if applicable (e.g. ISO 27001).
- **Tool-qualification** — for tools producing safety-critical
  output (e.g. DO-330).

Bottom row (sub-tiles):

- **Confirmed** — `— yes` / `— no`, whether the regime has been
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

A `Full report` link in the top-right of the spec-tree section
takes you to the project's full Report view (`/p/<slug>/report`),
which renders the spec-tree in much more detail along with all the
ConOps, Requirements, Design, Verification, and Hazards content.

## RECENT SESSIONS

The bottom section is a feed of the most recent harness sessions
for this project. A `View log` link in the section's top-right
takes you to the journal index (`/p/<slug>/journal`); each row in
the feed is a direct link to that session's journal entry.

Each row shows:

- The **session title** (left).
- An optional **phase tag** (right, e.g. `Review`, `QC`,
  `Decompose`) — present on sessions whose flow corresponds to a
  workflow phase; absent on sessions that don't (e.g. one-off
  remediation passes).
- The **session date** (`YYYY-MM-DD`).

Outcome (ok / partial / failed) is **not** shown on the dashboard;
to see whether a session succeeded, click into the entry or use
the Log view's filtering.

## A one-minute health check

Top-to-bottom scan:

1. **WORKFLOW.** Where in the pipeline is the project? Anything
   stuck at a phase for unusually long?
2. **Metric tiles.** Numbers reasonable? Big jumps since you last
   looked?
3. **HARNESS.** Timer running? Last cost normal? What's the current
   directive?
4. **SAFETY REGIME.** Set or not set? Confirmed? Coverage of hazards
   and requirements?
5. **SPEC TREE.** Any subsystem in an unexpected status?
6. **RECENT SESSIONS.** Anything recent and unexplained?

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
