# The Quality Gates view

`/p/<slug>/quality` shows the **Quality Gates** for a project — the
harness's machine-checkable thresholds that gate state-machine
progression. It's a focused view: gate state only. Other
quality-of-project signals (lint findings, orphan trace links,
ambiguous requirements) live in adjacent tools rather than this
page.

> **Note:** Quality Gates is **not** linked from the navigation rail.
> Navigate to it directly via the URL `/p/<slug>/quality`. This is
> intentional — gates are operationally important during harness
> sessions but rarely the right place to land for everyday review.

> **Prerequisites:** a Derive project. The page is empty until the
> harness has run gate evaluations for the project.

## Page header

The page has an h1 `Quality Gates` and the subtitle:

> *"Metrics evaluated against project-specific thresholds. Gates
> control state machine progression."*

Above the h1 is a `← Dashboard` back link.

## What gates are

A quality gate is a single yes/no condition the harness evaluates
between phases of its state machine. Examples:

- "All requirements have a QA score ≥ 70."
- "All hazards have at least one mitigating requirement."
- "No new orphan trace links since the last baseline."
- "Verification coverage ≥ 90 % across system requirements."

When a gate fails, the harness's state machine refuses to progress
to the next phase. This keeps the autonomous loop from accumulating
tech debt — it has to fix the failing gate before moving on.

When the project has gate evaluations, the page summarises each
gate's most recent run with its name, description, pass/fail
status, the metric vs the threshold, the phase that last
evaluated it, and a timestamp. The exact card layout is not
documented here yet — capture it on a project that has populated
gate data.

## Empty state

When no harness sessions have evaluated gates yet, the page shows:

> *No quality gate data available.*
>
> *Gates are evaluated for the active project during harness
> sessions.*

That empty state means "run a session" — it's not an error.

## How gates relate to the rest of the project

Gates are configured per project in the harness workflow config
(operator-only). Different regimes have different gates: a
SIL-2 project's gate might require certain document-level coverage
and verification activity counts that a research project's would
not.

Gates differ from these adjacent project-quality concepts:

| Concept                    | Where it lives                                 |
| -------------------------- | ---------------------------------------------- |
| **Quality gates**          | Harness, evaluated per session. This page.    |
| **QA score**               | AIRGen, per requirement. The Dashboard's metric tiles aggregate it. |
| **Orphan trace links**     | The `Orphans` tile on the project's Artifacts tab. |
| **Lint findings**          | AIRGen-side, via the AIRGen CLI / API.         |
| **Ambiguous requirements** | AIRGen QA panel per requirement.               |

So if you want a one-screen "is this project ready?" view, you'll
typically need to combine the Quality Gates page with the
Artifacts tab and AIRGen-side reports.

## Acting on a failed gate

When a gate is failing, the typical workflow:

1. **Read the gate detail.** What's the metric, what's the
   threshold, why is it short?
2. **Run the relevant report.** AIRGen-side reports are the
   usual source — exact CLI subcommands depend on your AIRGen
   version.
3. **Address the underlying data.** Edit requirements, add trace
   links, add verification activities, etc.
4. **Switch the WORKFLOW** on `/p/<slug>/control` to one that
   addresses the failing gate (e.g. `Validation` or `QC Pass`),
   or click **Force QC** for an immediate quality pass.
5. **Wait for the next session.** It re-evaluates the gate.
6. **Re-check the Quality Gates page.** When all gates pass, the
   harness can progress to the next phase.

## Why gates aren't in the nav rail

The page is operationally useful but the typical operator workflow
doesn't start there — it starts on the Dashboard or the Log. So
Derive keeps the rail short and lets you reach Quality Gates by
direct URL when the situation calls for it.

If your team uses gates heavily, consider bookmarking
`https://derive.airgen.studio/p/<slug>/quality` in your browser.

## Driving improvements from outside this page

Most of the work to *make* gates pass happens elsewhere:

- **Authoring / polishing requirements** — AIRGen web UI or
  CLI commands.
- **Adding trace links** — AIRGen UI or CLI.
- **Adding verification activities** — AIRGen-side.
- **Issuing directives to the harness** — Derive
  `/p/<slug>/control`.

Quality Gates tells you *what's failing*. The fix happens in the
upstream tools.

## What's next

- [Reading the per-project dashboard](./reading-the-per-project-dashboard.md)
- [Driving the autonomous loop](./driving-the-autonomous-loop.md)
- [Exports and document bundles](./exports-and-document-bundles.md)
- [Building a traceability matrix (AIRGen)](../../airgen/guides/building-a-traceability-matrix.md)
- [Understanding the QA score and EARS patterns (AIRGen)](../../airgen/guides/understanding-the-qa-score-and-ears-patterns.md)
