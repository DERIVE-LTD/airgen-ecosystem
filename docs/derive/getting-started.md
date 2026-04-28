# Getting started with Derive

Derive is the operator workbench for AIRGen projects. This guide walks
through opening a project, reading the dashboard, and using the
quality view, journal, and loop controls.

> **Prerequisites:**
>
> - Access to [derive.airgen.studio](https://derive.airgen.studio) (or
>   a self-hosted Derive instance)
> - A GitHub or Google account for OAuth sign-in
> - At least one project visible to your account

If you just want to look around without signing in, the public
read-only demo lives at `/demo`.

## 1. Sign in

Sign in with GitHub or Google. An admin must associate your account
with the projects you should see.

_TODO: screenshot of the sign-in screen._

## 2. Open a project

Each project lives at `/p/<slug>/`. From the project landing page,
the most useful starting tabs are:

- **Dashboard** — spec-tree, headline metrics, safety-regime card.
- **Quality** — lint findings, orphans, ambiguous requirements, gate state.
- **Journal** — session-log feed for this project.
- **Report** — long-form generated report.

_TODO: screenshot of the per-project dashboard._

## 3. Read the dashboard

The dashboard is the single-screen overview: the spec-tree on the left,
key metrics in the middle, and a safety-regime card that tells you
which standard the project is being evaluated against (tool-qualification,
SIL-rated, etc.). Use it to sanity-check that the project's data is
coherent before drilling into details.

## 4. Read the quality view

`/p/<slug>/quality` shows everything that needs human attention:

- Lint failures against AIRGen QA rules
- Requirements with no incoming or outgoing trace links (orphans)
- Ambiguous or under-specified requirements
- Whether the project meets the current quality gate

This is where you decide whether the autonomous loop's most recent
output is ready to ship, needs another iteration, or needs a directive.

## 5. Drive the autonomous loop

The autonomous loop is run by a Claude-driven orchestrator behind
Derive (the Claude Harness). Derive exposes the controls:

- `/control` — global pause / unpause and directive submission
- `/p/<slug>/control` — per-project loop controls

Use these to pause the loop while you review, submit a directive
("focus on hazard analysis next"), or unpause once you're happy.

_TODO: screenshot of the loop control panel._

## 6. Read the journal

Each loop session writes a markdown post to the journal. Derive renders
those posts at:

- `/journal` — cross-project feed
- `/p/<slug>/journal` — per-project feed
- `/p/<slug>/journal/<entry>` — single post

This is the human-readable narrative of what the loop did and why.

## 7. Export an artefact bundle

When you have a stable point — typically a baselined release — open
**Artifacts** or **Downloads** on the project. You can export:

- Word and PDF reports
- Excel exports of requirements
- Zipped artefact bundles for delivery or audit

## Next steps

- _TODO: link to "Reading the per-project dashboard"_
- _TODO: link to "The quality view: lint findings, orphans, gate state"_
- _TODO: link to "Working with baselines and artefact bundles"_
- _TODO: link to ecosystem overview_
