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

The page summarises each gate's most recent evaluation:

- **Name and description.**
- **Pass / fail status.**
- **The metric vs the threshold** (e.g. `QA score median: 67 / 70`).
- **The phase that last evaluated it.**
- **Timestamp** of the last evaluation.

When no harness sessions have run yet, the page shows:

> *No quality gate data available. Gates are evaluated for the
> active project during harness sessions.*

That empty state means "run a session" — it's not an error.

## How gates relate to the rest of the project

Gates are configured per project in the harness workflow config
(operator-only). Different regimes have different gates: a
SIL-2 project's gate might require certain document-level coverage
and verification activity counts that a research project's would
not.

Gates differ from these adjacent project-quality concepts:

| Concept           | Where it lives                              |
| ----------------- | ------------------------------------------- |
| **Quality gates** | Harness, evaluated per session. This page. |
| **QA score**      | AIRGen, per requirement. The dashboard's metric tiles aggregate it. |
| **Lint findings** | AIRGen `airgen lint`. Run separately; output to terminal or markdown report. |
| **Orphan trace links** | AIRGen `airgen report orphans`. Or via `airgen reqs filter`. |
| **Ambiguous requirements** | AIRGen QA panel per requirement, plus aggregate via `airgen report quality`. |

So if you want a one-screen "is this project ready?" view, you'll
typically need to combine the Quality Gates page with `airgen
report` outputs.

## Acting on a failed gate

When a gate is failing, the typical workflow:

1. **Read the gate detail.** What's the metric, what's the
   threshold, why is it short?
2. **Run the relevant AIRGen report.**
   - QA score gate failing → `airgen report quality <tenant>
     <project>`.
   - Coverage gate failing → `airgen verify matrix`.
   - Orphan gate failing → `airgen report orphans`.
3. **Address the underlying data.**
4. **Switch the WORKFLOW** on `/p/<slug>/control` to one that
   addresses the failing gate (e.g. a polish or QC workflow), or
   click **Force QC** for an immediate quality pass.
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
  `airgen reqs` CLI commands.
- **Adding trace links** — AIRGen UI or `airgen trace create`.
- **Adding verification activities** — AIRGen `airgen verify`
  commands.
- **Issuing directives to the harness** — Derive `/p/<slug>/control`.

Quality Gates tells you *what's failing*. The fix happens in the
upstream tools.

## What's next

- [Reading the per-project dashboard](./reading-the-per-project-dashboard.md)
- [Driving the autonomous loop](./driving-the-autonomous-loop.md)
- [Building a traceability matrix (AIRGen)](../../airgen/guides/building-a-traceability-matrix.md)
- [Understanding the QA score and EARS patterns (AIRGen)](../../airgen/guides/understanding-the-qa-score-and-ears-patterns.md)
