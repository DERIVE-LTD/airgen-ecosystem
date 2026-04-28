# Reading the per-project dashboard and spec-tree

The per-project dashboard at `/p/<slug>/dashboard` is Derive's
single-screen overview of a project. It surfaces the spec-tree, key
metrics, the safety regime, and pointers to the most recent agent
session output. This guide walks through what each panel means and
how to read the dashboard in a one-minute health-check.

> **Prerequisites:** access to a Derive instance and a project to
> open. If you've never used Derive, start with
> [the getting-started guide](../getting-started.md).

## The dashboard layout

```
┌─────────────────────┬───────────────────────────────────────────────┐
│                     │                                               │
│     SPEC-TREE       │       METRICS / SAFETY REGIME / RECENT        │
│   (left rail)       │                                               │
│                     │                                               │
└─────────────────────┴───────────────────────────────────────────────┘
```

The left rail is the spec-tree — the hierarchical decomposition of
the project. The right pane shows headline metrics, the safety-regime
card, and a "what changed recently" feed.

## The spec-tree

The spec-tree is built from substrate facts (typically `SPEC_TREE`,
`PART_OF`, and a few related predicates). It looks like:

```
Widget X
├── Power Subsystem
│   ├── Battery Pack
│   ├── Power Distribution Unit
│   └── Charging Module
├── Thermal Subsystem
│   ├── Thermal Loop
│   └── Thermal Controller
└── Comms Subsystem
    └── ...
```

Each node maps to a substrate entity scoped to `SE:<slug>`. Click a
node to scope the dashboard's metrics and feed to that subtree —
useful for drilling into one area without losing context.

The spec-tree is **regime-aware**. If your project is registered as
a tool-qualification project (e.g. a software tool that produces
safety-critical output), the spec-tree shows tool-qualification
nodes (the qualified tool, the qualification artefacts) rather than
SIL-rated subsystems. The schema adapts to what the project actually
is.

## The metrics row

Headline counters for the project (or the selected subtree):

- **Requirements** — total, broken down by document type.
- **Trace links** — total, broken down by link type.
- **QA score** — median across all requirements; a small sparkline
  shows the trend over recent baselines.
- **Verification coverage** — percentage of requirements with at
  least one verification activity, percentage with passed evidence.
- **Hazards** — total, broken down by severity (or SIL / DAL).

Hover any metric for the breakdown; click for the underlying list
(takes you into the relevant tab — `quality`, `report`, etc.).

## The safety-regime card

A card on the right tells you which standard the project is being
evaluated against. Examples:

- **Tool qualification** — the project is a tool whose qualification
  artefacts must meet a regulatory bar (e.g. DO-330).
- **SIL-2 system** — IEC 61508 SIL-2 system development.
- **ASIL-D system** — ISO 26262 ASIL-D automotive.
- **DAL-A system** — DO-178C DAL-A airborne software.
- **None** — research / non-regulated project.

The regime drives:

- Which lint rules apply (the safety summariser is regime-aware —
  tool-qualification projects don't get SIL-related warnings).
- Which artefacts are expected at the milestones.
- Which fields are required on hazards.

If the regime is wrong, change it from the project settings — every
other view will update accordingly.

## The "recent" feed

The right-hand feed shows the last few items from the per-project
journal — typically the most recent autonomous-loop session, plus any
recent baselines or artefact exports. Click an item to jump to its
detail page (a journal post, a baseline diff, a downloadable bundle).

Useful at-a-glance signals:

- A red icon on a journal entry means the session ended in failure
  (timeouts, parse errors, validation failures). Investigate before
  continuing.
- A green icon on a baseline means it passed all the gates at
  creation time.
- A pause icon at the top of the feed means the autonomous loop is
  currently paused.

## A one-minute health check

Run this on every project at the start of a working session:

1. **Glance at the metrics row.** Numbers reasonable? Trend up or
   flat?
2. **Glance at the safety regime card.** Still right?
3. **Look at the spec-tree.** Anything unexpected (e.g. a subtree
   added by an unattended autonomous run)?
4. **Read the latest journal entry.** What did the loop do
   recently? Anything weird?
5. **Check the loop status.** Paused, running, blocked?

Sixty seconds. If anything looks off, switch to the
[quality view](./the-quality-view.md) for details.

## The dashboard from the CLI

```sh
# Stats overview (closest CLI equivalent)
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
