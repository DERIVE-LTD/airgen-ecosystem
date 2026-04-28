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
| Per-project dashboard       | Spec-tree, key metrics, safety-regime card.                                      |
| Quality view                | Lint findings, orphans, ambiguous requirements, gate state.                      |
| Report viewer               | Long-form generated reports rendered inline.                                     |
| Session journal             | Cross-project and per-project session feeds rendered from markdown.              |
| Loop control panel          | Pause / unpause the autonomous loop and issue directives, per project or globally. |
| Artefacts and downloads     | Word, PDF, Excel exports; baseline bundles.                                      |
| Public demo                 | Read-only demo project at `/demo` for evaluation.                                |
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
├── /control/              Loop control panel (pause/unpause, directives)
├── /demo/                 Public read-only demo project
├── /journal/              Cross-project session-log feed
├── /login/                Sign-in
└── /p/[slug]/             Per-project workspace
    ├── artifacts/         Generated artefacts (Word/PDF, baselines)
    ├── control/           Per-project loop controls
    ├── dashboard/         Spec-tree, metrics, safety regime
    ├── downloads/         Bundle/PDF/Excel exports
    ├── init/              Bootstrap a fresh project
    ├── journal/           Per-project session feed
    ├── quality/           Lint, orphans, ambiguous reqs, gate state
    └── report/            Long-form report viewer
```

## Guides

- [Getting started](./getting-started.md) — first-run walkthrough
- [`guides/`](./guides/) — task-oriented guides _(coming soon)_

Planned topics:

- Reading the per-project dashboard and spec-tree
- The quality view: lint findings, orphans, gate state
- Driving the autonomous loop: pause, unpause, and directives
- Working with baselines and artefact bundles
- Managing per-project roles and user access
- Publishing a public read-only demo project
