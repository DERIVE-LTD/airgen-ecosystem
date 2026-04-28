# Publishing a public read-only demo project

Derive's `/demo` route is a public, no-login-required view of a
designated demo project. It is the simplest way to let prospective
customers, conference attendees, or open-source visitors see the
platform in action without provisioning accounts.

This guide walks through choosing a good demo project, configuring
it for public exposure, and the conventions that keep a demo useful
without leaking sensitive data.

> **Prerequisites:** admin role on the project you intend to expose,
> and tenant admin role to enable the demo route. Demo projects are
> exposed at `/demo` by default; they bypass auth for read-only
> access.

## What `/demo` shows

A demo project surfaces:

- The project dashboard.
- The spec-tree.
- The journal.
- The quality view.
- The report viewer.
- All read-only tabs.

It does **not** surface:

- Loop controls (pause / unpause / directives).
- Admin pages.
- User management.
- Sensitive configuration.
- The authenticated REST API.

Demo visitors see the project; they cannot interact with it.

## 1. Pick a good demo project

A useful demo project has:

- **Real-feeling content.** A toy project ("hello world") doesn't
  show the platform's value. Choose something domain-rich.
- **Sanitised content.** No customer names, no proprietary
  technology, no internal IP. Build a fictional system from public
  domain knowledge.
- **A passing quality gate.** A messy demo undersells you.
- **Recent autonomous-loop activity.** Visitors can read the journal
  and see the autonomous loop in action.
- **A safety regime.** Demonstrates the regime-aware features —
  hazard analysis, verification matrix, gates.

Existing projects in your tenant rarely meet all of these. Most
demos benefit from a **purpose-built fictional project** whose only
audience is demo visitors.

## 2. Build a sanitised demo project

A typical recipe:

1. Pick a fictional system that's familiar enough to be obvious but
   not so trivial it's boring. Real examples that work: a kids' RC
   airplane, a fusion-reactor control system, a forward-deployed
   medical device. Avoid: anything you actually work on.
2. Create a new project (`airgen projects create <tenant>
   --name "RC Airplane Demo"`) and set the safety regime
   appropriately.
3. Author 50–200 stakeholder + system requirements covering the
   golden path and a few hazards.
4. Run the autonomous loop (a couple of decompose / validate
   sessions) so the journal has interesting content.
5. Polish to a green quality gate — orphans resolved, lint clean,
   verification activities present.
6. Create an initial baseline (`v1.0`) so visitors can see baseline
   diffs.

A good demo project takes about a day to build and lasts for years.

## 3. Mark the project as the demo project

The demo route exposes one designated project. To mark a project
as the demo:

- **Tenant admin → Settings → Public demo project** (or equivalent
  in your version) — pick the project from a dropdown.

Some Derive deployments allow multiple demo projects (with the
`/demo` route showing a chooser). If yours does, surface the cleanest
one as the default.

## 4. Verify the public surface

Open `/demo` from a private browser window (no auth). Confirm:

- The dashboard loads and shows reasonable metrics.
- The spec-tree renders.
- The journal shows recent entries (and they read well — no leaked
  customer names!).
- The quality view is green.
- The report viewer renders the project report.
- Attempting any write action (or visiting `/p/<slug>/control`) is
  either blocked or redirects to login.

If you find anything sensitive on the public surface, fix it and
re-verify.

## 5. Embed in your marketing

Once `/demo` is solid, link it from:

- The Derive Ltd marketing site
  ([derive-ltd.co.uk](https://derive-ltd.co.uk)).
- Conference talks and slide decks.
- The repo README of your demo or example projects.
- LinkedIn posts and blog articles about the platform.

A direct link to `https://derive.airgen.studio/demo` lets readers
immediately see what you're describing.

## 6. Maintain the demo

A stale demo undersells the platform. A maintenance cadence:

- **Weekly.** Run an autonomous-loop session against the demo
  project so the journal stays fresh.
- **Monthly.** Add a new requirement or two, re-baseline if
  meaningful.
- **Quarterly.** Refresh the demo's README / talking points to
  match new platform features.
- **At releases.** Make sure the demo highlights the most recent
  Derive feature additions.

## Demo-as-test pattern

A useful side benefit: the demo project doubles as an integration
test for new Derive releases. Before a Derive deploy:

1. Run the demo through every public route in a private browser.
2. Run the demo through every authenticated route as an admin.
3. Trigger a fresh autonomous-loop session.
4. Check the dashboard, quality view, journal, baselines.

If anything's broken, the demo is the first thing visitors see — so
catching it before deploy is doubly important.

## What's next

- [Managing per-project roles and user access](./managing-per-project-roles-and-user-access.md)
- [Reading the per-project dashboard](./reading-the-per-project-dashboard.md)
- [Working with baselines and artefact bundles](./working-with-baselines-and-artefact-bundles.md)
