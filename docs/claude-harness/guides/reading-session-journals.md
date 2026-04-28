# Reading session journals

Each harness session writes a markdown journal post — a narrative of
what the session did, why, and what gates it passed or failed.
Journal posts are the operator's primary way of understanding what
the autonomous loop has been up to between reviews.

This guide explains the journal's structure, how to read a single
post efficiently, and the cross-project journal patterns worth
watching.

> **Prerequisites:** access to a Derive project. The journal is
> exposed at `/journal` (cross-project) and `/p/<slug>/journal`
> (per-project).

## Where the journal lives

Each harness session writes a markdown file to a journal directory
on the host that runs the harness (filesystem path varies by
deployment; typical: `/opt/uht-loop/journal/src/content/posts/`).
Derive reads that directory via a filesystem glob (with a few-minute
cache) and renders the markdown in two views:

- `/journal` — cross-project feed, newest first, all projects.
- `/p/<slug>/journal` — feed scoped to one project.
- `/p/<slug>/journal/<entry>` — single post.

The markdown source is the canonical record. Derive's rendering is a
view; if you ever need raw access (audit, compliance, archival),
read the markdown files directly.

## The structure of a journal post

A typical post looks like:

```
---
project: widget-x
session: 2026-04-28T14:32:00Z
phase: decompose
status: ok
duration: 4m 12s
turns: 18
gates:
  parser: pass
  schema: pass
  trace: pass
  qa: pass
---

# Decompose session — widget-x

## Directive

Resolve orphan trace links in `system-requirements`...

## What I did

I read the current orphan list (12 entries) and proposed parent
requirements in `stakeholder-requirements`. For each orphan:

- SYS-014 → STK-005 (derives) — proposed
- SYS-016 → STK-007 (derives) — proposed
- ...

## What I produced

- 12 candidate trace links (added to AIRGen as candidates)
- 0 new requirements
- 0 substrate facts

## Gates

All passed.

## Notes

Three orphans (SYS-022, SYS-023, SYS-024) referenced concepts that
don't exist in stakeholder-requirements. I left these as orphans and
flagged them in the candidates list.
```

The frontmatter is structured metadata. The body is human-readable
narrative. Both are useful — the frontmatter for machine queries,
the body for operator review.

## Reading a single post

A scan-read of one post takes under a minute:

1. **Status banner.** Look at the frontmatter `status`. Values:
   - `ok` — session completed, all gates passed.
   - `partial` — session completed, some gates failed.
   - `failed` — session ended in error.
   - `interrupted` — session hit turn budget without finishing.
2. **Directive recap.** Did the loop interpret the directive
   correctly?
3. **What I did.** Skim — does the action match the directive?
4. **What I produced.** Counts of candidates, facts, etc. Match
   expectations?
5. **Gates.** Any failed gates need investigating.
6. **Notes.** This is where the loop flags things it couldn't
   resolve. Read carefully.

## Reading the cross-project feed

`/journal` is useful at the start of a working day:

- Sort by status, look for any `failed` or `partial` posts.
- Read those first — they're action items.
- Look at the volume of `ok` posts. Steady-state activity, or a
  surge?
- Check for a project that's *missing* from the feed — a project
  that hasn't logged a session in days might have a paused or
  broken loop.

The feed is not a substitute for the per-project quality view —
it's a triage layer.

## Reading the per-project feed

`/p/<slug>/journal` is where you'd spend ten minutes during a
weekly review:

1. Walk the last 5–10 posts in order.
2. For each, confirm the directive was acted on.
3. Note any "I left these for human review" flags.
4. Look at the trend — are sessions getting better at the focus, or
   are they cycling on the same issues?

## Common patterns to recognise

A few patterns worth watching for, and what they usually mean:

### A run of `partial` posts

The gate is failing repeatedly. Either:

- The output is genuinely insufficient (raise the directive's bar
  or re-run with more context).
- The gate is too strict for the current state (lower the gate in
  the workflow config or accept partial output for now).

### Empty `What I produced` sections

The session ran but didn't produce concrete output. Usually means:

- The directive was too narrow ("if X, do Y" with X never met).
- The project state already satisfies the directive (good — submit
  the next directive).

### `Notes` flagging recurring issues

If the loop keeps flagging the same issue ("substrate predicate
HAS_FUNCTION used inconsistently"), it's signal that:

- The vocabulary needs alignment across the project (a one-off
  cleanup directive).
- The workflow config's predicate validation rules need adjusting.

### Sessions that "interrupted" repeatedly

The turn budget is too tight for the work. Either narrow the
directive or raise the budget for that phase.

### A session that says it "couldn't reach AIRGen"

Infrastructure issue, not a content issue. Check the AIRGen API
health from the harness host. Likely transient; the next scheduled
session will retry.

## Searching the journal

For older posts, the journal supports text search:

- **UI** — search box at `/journal`.
- **Filesystem** — the markdown files are grep-able. For
  long-running projects:

  ```sh
  grep -l "validate" /opt/uht-loop/journal/src/content/posts/widget-x-*
  ```

- **CLI / scripting** — read the markdown files; the YAML
  frontmatter is machine-parseable.

## Journal-as-audit

Journal posts are immutable. A session writes its post once at the
end and never edits it. This makes the journal an audit trail —
useful for:

- **Regulatory review.** "What did the autonomous tooling do, and
  when?" answered by the journal.
- **Post-incident.** When a project's data goes wrong, walking
  back through journal posts is how you find the session that
  introduced the bad state.
- **Process improvement.** Reading a month of journals tells you
  which directives produced good work and which didn't.

Keep the journal directory backed up alongside the AIRGen and
substrate data — it's part of the project's record.

## What's next

- [Understanding a harness session](./understanding-a-harness-session.md)
- [Writing effective directives](./writing-effective-directives.md)
- [The relationship between harness sessions, AIRGen baselines, and substrate facts](./relationship-between-sessions-baselines-and-substrate-facts.md)
