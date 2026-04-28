# Driving the autonomous loop

Derive's Control Panel is the operator interface to the harness — the
autonomous engine that runs the systems-engineering pipeline. The
harness has no UI of its own; the Control Panel is where you set
the project's status, mode, and current workflow, and where you
manually trigger sessions when needed.

This guide covers the per-project Control Panel at
`/p/<slug>/control`. A separate global Control Panel at `/control`
is used by Derive instance admins to manage many projects at once;
operator accounts without admin rights are redirected to the
projects landing page when they hit that URL.

> **Prerequisites:** access to a Derive instance with permission to
> control the loop. Loop-control rights are typically restricted to
> senior engineers or admins; demo / read-only users do not see the
> Control tab in the project rail.

## Page layout

The page header is the h1 `Control Panel` with the subtitle
*"Manage the autonomous engineering loop"*. Below it, six stacked
sections in this order:

1. **Project card** — the three master controls (no h2; uses an
   inline `PROJECT` label).
2. **Status strip** — live state (no h2).
3. **Directives** — three action buttons.
4. **Workflow** — manual phase stepper.
5. **EMAIL NOTIFICATIONS** — per-project subscribe form.
6. **ACTIVITY** — operator-action audit log.

Section heading casing is mixed in the UI: `Directives` and
`Workflow` are Title Case, `EMAIL NOTIFICATIONS` and `ACTIVITY` are
ALL CAPS. This guide mirrors the UI casing so readers can find
each section by sight.

## The Project card — three master controls

Three controls live in a card at the top of the page:

| Control      | Values                                | Meaning                                                |
| ------------ | ------------------------------------- | ------------------------------------------------------ |
| **STATUS**   | `Active` / `Inactive` (toggle)        | Whether the harness will pick this project up at all.  |
| **MODE**     | `Auto` / `Manual` (toggle)            | Auto = scheduled sessions; Manual = only on demand.    |
| **WORKFLOW** | Dropdown — see below                  | Which workflow runs when the harness next acts.        |

The seven workflow options are:

- `None`
- `Full Decomposition`
- `Review & Update`
- `Red Team Only`
- `Validation`
- `QC Pass`
- `Re-decompose`

A **workflow** is a multi-phase pipeline (e.g. `Full Decomposition`
takes the project from Concept through Complete). A workflow is
made up of **flows** — `tech-author`, `validate`, `red-team`, etc.
The Dashboard's `LAST FLOW` tile reports the most recent flow that
ran inside whatever workflow the loop is currently executing; the
WORKFLOW dropdown here picks the workflow itself, not a flow.

Switching STATUS to `Inactive` is how you take a project out of the
loop entirely. MODE = `Manual` leaves the project in the loop but
prevents auto-scheduled sessions. The WORKFLOW dropdown picks which
multi-phase pipeline runs next.

A small pill in the top-right of the card surfaces the effective
operating mode (`Auto` in green when the loop is actively running
this project; `Manual` otherwise).

## The Status strip — live state

Four fields:

| Field          | What it shows                                                  |
| -------------- | -------------------------------------------------------------- |
| **TIMER**      | `running` or `paused`. The harness scheduler state.            |
| **STATE**      | The current state-machine state (e.g. `published`, `decomposing`). |
| **DIRECTIVE**  | The current standing directive (`PAUSE`, `RESUME`, …).         |
| **SESSIONS**   | Lifetime session count on this Derive instance.                |

The `SESSIONS` count and the Dashboard's HARNESS strip both report
**instance-wide** harness activity, not per-project counters — the
same value appears on every project's Control panel. For a
project-specific session list, use the Activity feed below or the
Log (journal) view.

Read this strip before issuing a new directive to confirm where
the loop is now.

## Directives — three buttons

| Button       | Effect                                                          | Activity-feed verb                        |
| ------------ | --------------------------------------------------------------- | ----------------------------------------- |
| **Resume**   | Lift any pause; let the scheduler run sessions normally.        | `Resumed the autonomous loop`             |
| **Run Now**  | Trigger a session immediately, without waiting for the schedule. | `Triggered immediate session`             |
| **Force QC** | Force a Quality Check / QC phase as the next session.           | (presumed `Forced QC`)                    |

Visual treatment is meaningful: `Resume` is rendered green,
`Run Now` is amber, and `Force QC` is muted. The colour cues match
the destructiveness of the action — green to lift the gate, amber
to spend a session right now.

The current `DIRECTIVE` field in the status strip reflects which
directive is active. Operators issue a new directive by clicking
one of these three buttons.

> **Important difference vs the conceptual story.** Directives in
> Derive are not free-text instructions — they're a small fixed
> set of state-machine signals. The free-text "what should I work
> on next" guidance happens via the harness's workflow config and
> by adjusting the project STATUS / MODE / WORKFLOW selectors. For
> ad-hoc free-text guidance, use the harness from Claude Desktop's
> MCP surface (where you can pass conversational context) rather
> than this UI.

## Workflow — manual phase stepper

A vertical list of the harness's nine pipeline phases, with `←
Back` and `Next →` buttons in the top-right of the card:

- Idle
- Concept
- Scaffold
- Decompose
- First Pass
- QC Reviewed
- Validated
- Red Teamed
- Complete

Each row shows the phase name and the flow tag attached to that
phase (`concept`, `scaffold`, `decompose`, `qc`, `validate`,
`review`, `red-team`, `current`). Unlike the Dashboard's WORKFLOW
card, **the indicators here are stateless** — every row's status
dot is empty regardless of whether the phase has run. This card is
a stepper, not a status view: the dots tell you nothing about
progress. To see which phase is current and which are complete,
use the Dashboard.

In Auto mode the harness handles phase transitions itself. The `←
Back` and `Next →` buttons are for Manual mode and operator
overrides — useful when the auto-scheduler is stuck on a phase or
when you're debugging.

## EMAIL NOTIFICATIONS

A simple subscribe form per project: an email input with the
placeholder `your@email.com` and a `Subscribe` button. Submissions
are scoped to this project — *"Receive session logs by email when
this project completes a session or encounters an error"*. The
global Control Panel has its own subscription form for
cross-project notifications (admin-only).

## ACTIVITY

A timestamped audit log of **operator actions** on this project.
Newest first; each row is dense with no separator between entries.

Format:

```
⟡  Steven Holland — Paused the autonomous loop      Apr 27, 10:18:33 PM
⟡  Steven Holland — Triggered immediate session     Apr 27, 10:10:31 PM
⟡  Steven Holland — Resumed the autonomous loop     Apr 27, 05:29:07 PM
…
```

Each row carries the `⟡` glyph (the same icon as the rail's
Control nav item), the operator name, an em-dash, the action verb,
and a full timestamp including seconds. The action verb maps from
the Directive button (and from the master toggles), not always
identically — `Run Now` records as `Triggered immediate session`,
for example.

Note: **harness/session events do not appear here**. The activity
feed is operator-only. To see what the harness did between
operator clicks, open the Log (journal) view.

## A working session pattern

A productive working session with the loop:

1. **Open `/p/<slug>/control` and `/p/<slug>/journal` (Log) side
   by side.**
2. **Read the most recent Log post.** Understand what the loop
   just did and why.
3. **Click Resume.** Or set STATUS to `Inactive` if you want to
   investigate without scheduled sessions firing.
4. **Walk the candidates.** In AIRGen
   (`airgen ai candidates`), accept the good ones, reject the bad
   ones, edit the nearly-right ones.
5. **Pick the next workflow** from the WORKFLOW dropdown if you
   want a different focus (e.g. switch from `Full Decomposition`
   to `Validation`).
6. **Click Resume** (or **Run Now** for an immediate session).
7. **Move on.** Come back when the loop has produced more output.

For free-text guidance ("focus on hazards next"), the path is
through the harness's MCP surface, not the Control Panel buttons.

## Loop failure modes

Sessions sometimes fail. The Log post tells you why. Common
failures:

- **Timeout.** A session ran too long. Either raise the timeout
  in the workflow config or split the work.
- **Parser error.** The session's SysML output didn't parse.
  Check the dry-run output in the Log post.
- **Validation gate.** A session failed a pre-commit check.
  Same investigation.
- **API error.** Claude API rate limit, network. The harness
  retries automatically.

When in doubt, set STATUS to `Inactive` temporarily and read the
Log post end-to-end.

## Cost management

Each session costs Claude API tokens. The Dashboard's HARNESS card
reports the most recent session's cost (`Last Cost`), but that
value is **instance-wide** — it's the cost of the most recent
session anywhere on the harness, not specifically this project's.
The Activity feed shows operator actions only, without per-row
cost.

Per-project cost tracking is currently CLI-only:

```sh
airgen activity list <tenant> <slug> --limit 20
```

Strategies for keeping spend in line:

- **Set STATUS to Inactive on dormant projects.** No scheduled
  sessions, no cost.
- **Use Manual MODE for slow-moving projects.** Run sessions when
  you're paying attention.
- **Pick a tighter WORKFLOW.** A scoped workflow (e.g. `QC Pass`,
  `Validation`) runs shorter sessions than `Full Decomposition`.
- **Watch the Activity feed for repeated `Run Now` cycles.** A
  manual-rerun loop usually means a stuck phase that needs human
  attention rather than another session.

## What's next

- [Reading session journals](../../claude-harness/guides/reading-session-journals.md)
- [Exports and document bundles](./exports-and-document-bundles.md)
- [The Quality Gates view](./the-quality-view.md)
