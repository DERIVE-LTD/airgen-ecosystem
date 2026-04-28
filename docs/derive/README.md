# Derive

**Operator-facing systems-engineering workbench for AIRGen projects.**

Derive is the cockpit for the autonomous systems-engineering pipeline.
It reads requirements, baselines, traceability, and substrate facts
from upstream stores and presents them as a per-project workspace —
spec-tree dashboard, session journal, quality view, report viewer, and
controls for the autonomous loop driven by the Claude Harness.

- **Hosted:** [derive.airgen.studio](https://derive.airgen.studio)
- **Stack:** SvelteKit 2 on Svelte 5 (runes), Vite 7, Tailwind 4,
  GitHub / Google OAuth (arctic + oslo), PWA via `@vite-pwa/sveltekit`,
  client-side IndexedDB persistence, web-push notifications

## What Derive does

Derive does **not** own the model. It reads from upstream stores and
turns that data into something an engineer can drive.

| Capability                  | Summary                                                                          |
| --------------------------- | -------------------------------------------------------------------------------- |
| Per-project dashboard       | Workflow state machine, eight metric tiles, harness telemetry, safety regime, spec-tree, recent sessions. |
| Quality Gates               | Harness gate state per project (direct URL `/quality`; not in nav rail).         |
| Report viewer               | Long-form generated System Decomposition Report rendered inline.                 |
| Session Log                 | Per-project and cross-project session-log feed (URL `/journal`, UI label "Log"). |
| Control Panel               | STATUS / MODE / WORKFLOW selectors, three directive buttons (Resume, Run Now, Force QC), manual phase stepping, email subscriptions, activity audit. |
| Artefacts and downloads     | Word, PDF, Excel exports; baseline bundles.                                      |
| Public demo                 | `/demo` route for evaluation (resolves to a designated demo project).            |
| Admin                       | Project and user management, per-project roles, analytics.                       |
| OAuth + PWA                 | GitHub and Google sign-in; installable as a PWA with push notifications.         |

## Data sources

| Store           | What it provides                                                  |
| --------------- | ----------------------------------------------------------------- |
| AIRGen API      | Projects, requirements, trace links, baselines.                   |
| `uht-substrate` | Entities and facts (`PART_OF`, `SPEC_TREE`, hazards, etc.).       |
| SurrealDB       | Session records and harness state.                                |
| Filesystem      | Session-log markdown produced by the autonomous loop.             |

## Routes

```
src/routes/
├── /                      Landing
├── /admin/                Admin tools (project/user management, analytics)
├── /auth/                 OAuth + session
├── /control/              Global Control Panel — harness status, all projects
├── /demo/                 Public demo route — resolves to a designated project
├── /journal/              Cross-project session-log feed (UI label "Log")
├── /login/                Sign-in
└── /p/[slug]/             Per-project workspace
    ├── artifacts/         Generated artefacts (Word/PDF, baselines)
    ├── control/           Per-project Control Panel
    ├── dashboard/         Project overview — workflow, metrics, harness, regime, spec-tree
    ├── downloads/         Bundle/PDF/Excel exports
    ├── init/              Bootstrap a fresh project
    ├── journal/           Per-project Session Log (UI label "Log")
    ├── quality/           Quality Gates — harness gate state (not in nav rail)
    └── report/            Long-form System Decomposition Report
```

Per-project navigation rail items: **Dashboard**, **Artifacts**,
**Report**, **Log** (which routes to `/journal`), **Control**,
**Downloads**. The `/quality` and `/init` routes are reachable by
direct URL but are not surfaced in the rail.

## Guides

Start here:

- [Getting started](./getting-started.md) — first-run walkthrough

In-depth guides:

- [Reading the per-project dashboard and spec-tree](./guides/reading-the-per-project-dashboard.md)
- [The Quality Gates view](./guides/the-quality-view.md)
- [Driving the autonomous loop: STATUS, MODE, and directives](./guides/driving-the-autonomous-loop.md)
- [Working with baselines and artefact bundles](./guides/working-with-baselines-and-artefact-bundles.md)
- [Managing per-project roles and user access](./guides/managing-per-project-roles-and-user-access.md)
- [Publishing a public read-only demo project](./guides/publishing-a-public-read-only-demo-project.md)
