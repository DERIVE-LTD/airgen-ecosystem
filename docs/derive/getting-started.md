# Getting started with Derive

Derive is the operator workbench for AIRGen projects. This guide walks
through opening a project, reading the dashboard, and using the
Quality Gates view, Log, and Control Panel.

> **Prerequisites:**
>
> - Access to [derive.airgen.studio](https://derive.airgen.studio) (or
>   a self-hosted Derive instance)
> - A GitHub or Google account for OAuth sign-in
> - At least one project visible to your account

If you just want to look around without signing in, the public
read-only demo lives at [`/demo`](https://derive.airgen.studio/demo).

## 1. Sign in

Sign in with GitHub or Google. Tenant admins associate accounts
with the projects they should see; if you have no projects after
signing in, contact your admin.

## 2. Open a project

Each project lives at `/p/<slug>/`. The per-project navigation rail
exposes six tabs (five for read-only / demo users — Control is
hidden):

- **Dashboard** — spec-tree, headline metrics, safety-regime card.
- **Artifacts** — content browser for requirements, documents,
  diagrams, traces, and orphans.
- **Report** — long-form generated System Decomposition Report.
- **Log** — session-log feed for this project (route `/journal`).
- **Control** — operator controls for the autonomous loop
  (signed-in operators only).
- **Downloads** — exports and document bundles.

There's also a **Quality Gates** view at `/p/<slug>/quality` —
useful but not surfaced in the rail.

## 3. Read the dashboard

The dashboard is the single-screen overview: a workflow state
machine, eight metric tiles, the harness telemetry strip, a
safety-regime card, the spec-tree, and a Recent Sessions feed. Use
it to sanity-check that the project's data is coherent before
drilling into details.

For the full walkthrough, see
[Reading the per-project dashboard](./guides/reading-the-per-project-dashboard.md).

## 4. Read the Quality Gates view

`/p/<slug>/quality` shows the harness's machine-checkable
thresholds that gate state-machine progression. When all gates
pass, the autonomous loop can advance phases. When one fails, it
halts until the underlying issue is fixed.

For details, see
[The Quality Gates view](./guides/the-quality-view.md).

## 5. Drive the autonomous loop

The autonomous loop is run by a Claude-driven orchestrator behind
Derive (the Claude Harness). Derive exposes the controls in the
per-project Control Panel at `/p/<slug>/control`:

- **STATUS** toggle (Active / Inactive) — whether the loop picks
  this project up.
- **MODE** toggle (Auto / Manual) — scheduled vs operator-triggered.
- **WORKFLOW** dropdown — which workflow runs.
- Three directive buttons: **Resume**, **Run Now**, **Force QC**.
- Manual phase stepping with **← Back** / **Next →** for the
  state machine.
- Email subscription form for session log notifications.
- Activity audit feed.

A global Control Panel at `/control` exists for tenant admins.

For details, see
[Driving the autonomous loop](./guides/driving-the-autonomous-loop.md).

## 6. Read the Log

Each loop session writes a markdown post that Derive renders in the
Session Log (the nav-rail item is labelled "Log"; the route is still
`/journal`):

- `/journal` — cross-project feed
- `/p/<slug>/journal` — per-project feed
- `/p/<slug>/journal/<entry>` — single post

This is the human-readable narrative of what the loop did and why.

## 7. Export

When you need to share the project with someone outside Derive —
a customer, regulator, or external review tool — open
`/p/<slug>/downloads` (the **Downloads** tab in the rail). The
Exports page offers:

- A **Complete Specification Bundle** (ZIP — cover letter, ISO
  PDFs, Excel + ReqIF, engineering log)
- **Full Project** as Excel, ReqIF, or PDF
- **ISO 15289 Documents** per information item
  (Concept of Operations / Requirements Specification / Design
  Description / Verification Plan), each in `.md`, `.pdf`,
  `.docx`, and `.json`
- **Per Document** exports of individual requirement documents
- The **Engineering Log** as markdown
- An **Offline Library** for items saved for offline access

For details, see
[Exports and document bundles](./guides/exports-and-document-bundles.md).

## Next steps

- [Reading the per-project dashboard](./guides/reading-the-per-project-dashboard.md)
- [The Quality Gates view](./guides/the-quality-view.md)
- [Driving the autonomous loop](./guides/driving-the-autonomous-loop.md)
- [Exports and document bundles](./guides/exports-and-document-bundles.md)
- [The public read-only demo](./guides/publishing-a-public-read-only-demo-project.md)
- [Ecosystem overview](../ecosystem/overview.md)
