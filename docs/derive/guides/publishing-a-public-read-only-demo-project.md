# The public read-only demo

Derive's `/demo` route is a public, no-login-required view of a
designated demo project. It's the simplest way to let prospective
customers, conference attendees, or open-source visitors see the
platform in action without provisioning accounts.

This guide explains what the demo route does, what visitors can
and can't do once they're on it, and how to keep the underlying
demo project useful.

> **Note:** which project the demo route resolves to is configured
> at deployment time, not via the user UI. The Settings page
> available to operators surfaces only push-notification toggles
> — there is no self-service "make this my demo project" affordance
> at the time of writing.

## What `/demo` does

Visiting `https://derive.airgen.studio/demo` redirects an unauthenticated
visitor to the configured demo project's Report view (e.g.
`/p/<demo-slug>/report`) and signs them in as a synthetic
`Demo User` with read-only access. From there they have the
project nav rail with five tabs — **Dashboard**, **Artifacts**,
**Report**, **Log**, **Downloads** — and can navigate freely
between them.

The demo user does **not** see:

- The **Control** tab. The autonomous loop is operator-only.
- The **Sign out** behaves as expected, but signing back in
  routes through the normal `/login` flow.
- Any admin pages.
- Any other project on the instance.

A demo visitor can read the project, walk the spec-tree and
diagrams, browse the Log, take screenshots, and pull exports from
the Downloads page. They cannot author, modify, or drive the loop.

## Anatomy of a good demo project

The demo project is the only thing visitors see, so make it the
best it can be. A useful demo project has:

- **Real-feeling content.** A toy project ("hello world") doesn't
  show the platform's value. Pick a domain that's recognisable
  but not real-customer.
- **Sanitised content.** No customer names, no proprietary
  technology, no internal IP. Build a fictional system from
  public-domain knowledge.
- **A complete decomposition.** STATUS `COMPLETE` looks better
  than half-finished. A visitor reading a partial project will
  assume the platform produces partial output.
- **Recent autonomous-loop activity.** The Log should have
  interesting entries close to today's date so the platform
  reads as actively used.
- **A safety regime, even a light one.** The SAFETY REGIME card
  is a distinctive Derive feature — leaving it `NONE` undersells
  the platform.
- **A complete document set in Downloads.** Visitors who want
  receipts will hit the Exports page — the Complete
  Specification Bundle is the artefact most likely to be
  downloaded by an evaluator.

A purpose-built fictional project usually beats reusing an
existing one. Most real projects fail one of the criteria above.

## Building a sanitised demo project

A typical recipe:

1. Pick a fictional system that's familiar enough to be obvious
   but not so trivial it's boring. Real examples that work: a
   kids' RC airplane, a fusion-reactor control system, a
   forward-deployed medical device. Avoid: anything you actually
   work on.
2. Create the project (e.g. `airgen projects create <tenant>
   --name "RC Airplane Demo"`) and set the safety regime
   appropriately.
3. Author 50–200 stakeholder + system requirements covering the
   golden path and a few hazards.
4. Run the autonomous loop (a couple of decompose / validate /
   review sessions) so the journal has substantive content.
5. Polish: orphans resolved, lint clean, verification activities
   present, dashboard tiles non-zero.
6. Pre-generate the Complete Specification Bundle by visiting the
   Downloads page once — the bundle is then cached for the next
   visitor.

A good demo project takes about a day to build and lasts for
years.

## Verifying the public surface

After any change to the demo project, open `/demo` from a private
browser window and confirm:

- The dashboard loads with reasonable metrics.
- The spec-tree renders.
- The Log shows recent entries that read well (no leaked
  customer names!).
- The Report view renders and is readable end-to-end.
- The Downloads page generates its bundles successfully.
- Attempting any write action (e.g. visiting `/p/<slug>/control`)
  is blocked or redirects.

If you find anything sensitive on the public surface, fix it and
re-verify.

## Linking to the demo

Once `/demo` is solid, link it from:

- The Derive Ltd marketing site
  ([derive-ltd.co.uk](https://derive-ltd.co.uk)).
- Conference talks and slide decks.
- Repo READMEs that reference Derive.
- Posts and blog articles about the platform.

A direct link to `https://derive.airgen.studio/demo` lets readers
immediately see what you're describing.

## Keeping the demo fresh

A stale demo undersells the platform. A maintenance cadence:

- **Weekly.** Run an autonomous-loop session against the demo
  project so the Log and dashboard stay current.
- **Monthly.** Add a new requirement or two; pre-generate the
  Complete Specification Bundle on the Downloads page.
- **Quarterly.** Refresh talking points to match new platform
  features.
- **At Derive releases.** Walk the demo through every public
  route — if anything's broken, this is what visitors will see
  first.

## Demo-as-test

A useful side benefit: the demo project doubles as an integration
test for new Derive releases. Before a deploy, walk the demo as a
visitor (private browser → `/demo`) and as an operator (signed in
to the same project). Catching breakage on the demo is doubly
important because it's the platform's public face.

## What's next

- [Reading the per-project dashboard](./reading-the-per-project-dashboard.md)
- [Exports and document bundles](./exports-and-document-bundles.md)
- [Driving the autonomous loop](./driving-the-autonomous-loop.md)
