# Driving the autonomous loop

Derive's Control Panel is the operator interface to the harness — the
autonomous engine that runs the systems-engineering pipeline. The
harness has no UI of its own; the Control Panel is where you set
the project's status, mode, and current directive, and where you
manually trigger sessions when needed.

This guide walks through both the global panel (`/control`) and the
per-project panel (`/p/<slug>/control`).

> **Prerequisites:** access to a Derive instance with permission to
> control the loop. Loop-control rights are typically restricted to
> senior engineers or admins.

## The two control surfaces

### `/control` — global

The global panel lists every project the harness operates against
(42 projects on a typical install). It has these sections:

- **Control Panel** — header.
- **Harness Status** — overall harness scheduler state.
- **Active Project** — which project the harness is currently
  acting on.
- **Projects (N)** — full list with each project's current status.
- **Email Notifications** — global subscription form.

Use the global panel for managing many projects at once or for
checking which project is currently being worked on.

### `/p/<slug>/control` — per-project

For one project, the Control Panel exposes everything you need to
direct the loop's behaviour for that project. The sections are:

1. **Project card** — the three master controls.
2. **Status strip** — live state.
3. **Directives** — the three action buttons.
4. **Workflow** — manual phase stepping.
5. **Email Notifications** — per-project subscription.
6. **Activity** — timestamped audit log.

This is where you'll spend most of your loop-control time.

## The Project card — three master controls

Three controls live in a card at the top of the page:

| Control      | Values            | Meaning                                                |
| ------------ | ----------------- | ------------------------------------------------------ |
| **STATUS**   | Active / Inactive | Whether the harness will pick this project up at all.  |
| **MODE**     | Auto / Manual     | Auto = scheduled sessions; Manual = only on demand.    |
| **WORKFLOW** | Dropdown          | Which workflow runs (e.g. `Full Decomposition`, `Tech Author`, `Validation`). |

Switching STATUS to Inactive is how you take a project out of the
loop entirely. MODE = Manual leaves the project in the loop but
prevents auto-scheduled sessions. The WORKFLOW dropdown picks which
multi-phase pipeline the loop runs.

A small pill in the corner of the card surfaces the effective
operating mode (e.g. **Auto** in green when the loop is actively
running this project).

## The Status strip — live state

Four fields:

| Field      | What it shows                                                |
| ---------- | ------------------------------------------------------------ |
| **TIMER**  | `running` or `paused`. Whether the harness scheduler is ticking. |
| **STATE**  | The current state machine state (e.g. `published`, `decomposing`). |
| **DIRECTIVE** | The current standing directive (`PAUSE`, `RESUME`, …). |
| **SESSIONS** | Lifetime session count for this project. |

Read this before issuing a new directive to confirm where the loop
is now.

## The Directives section — three buttons

Despite the name, **Directives is button-driven**, not free-text.
There are three actions:

| Button       | Effect                                                          |
| ------------ | --------------------------------------------------------------- |
| **Resume**   | Lift any pause; let the scheduler run sessions normally.        |
| **Run Now**  | Trigger a session immediately, without waiting for the schedule. |
| **Force QC** | Force a Quality Check / QC phase as the next session.           |

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

## The Workflow section — manual phase stepping

A visual state-machine showing the phases:

```
Idle  →  Concept  →  Scaffold  →  Decompose  →  First Pass
     →  QC Reviewed  →  Validated  →  Red Teamed  →  Complete
```

With **← Back** and **Next →** buttons in the corner that let an
operator manually step the project through phases — useful when
the auto-scheduler isn't picking up a phase you want, or when
you're debugging.

In Auto mode the harness handles transitions itself; the manual
buttons are for Manual mode and operator overrides.

## Email Notifications

A simple subscribe form per project — receive session logs by
email when this project completes a session or hits an error.
Useful for projects you want to monitor without keeping the tab
open.

The global `/control` page has the same subscription mechanism for
cross-project notifications.

## Activity feed

A timestamped audit log of operator actions and harness events:

```
Steven Holland — Paused the autonomous loop      Apr 27, 10:18 PM
Steven Holland — Forced QC                       Apr 27, 09:42 PM
Harness — Session 916 completed (tech-author)    Apr 27, 09:35 PM
…
```

A useful shorthand for "who did what, when?" — particularly
during shift handovers.

## A working session pattern

A productive working session with the loop:

1. **Open `/p/<slug>/control` and `/p/<slug>/journal` (Log) side
   by side.**
2. **Read the most recent Log post.** Understand what the loop
   just did and why.
3. **Click Resume.** Or Pause via the STATUS toggle if you want
   to investigate.
4. **Walk the candidates.** In AIRGen
   (`airgen ai candidates`), accept the good ones, reject the
   bad ones, edit the nearly-right ones.
5. **Pick the next workflow** from the WORKFLOW dropdown if you
   want a different focus (e.g. switch from `Full Decomposition`
   to `Tech Author`).
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

When in doubt, set STATUS to Inactive temporarily and read the
Log post end-to-end.

## Cost management

Each session costs Claude API tokens. The HARNESS card on the
project Dashboard shows the **Last Cost** field, and the Activity
feed on `/control` accumulates them over time.

Strategies:

- **Set Inactive on dormant projects.** No scheduled sessions,
  no cost.
- **Use Manual mode for slow-moving projects.** Run sessions when
  you're paying attention.
- **Pick a tighter WORKFLOW.** A scoped workflow runs shorter
  sessions than `Full Decomposition`.
- **Watch the Activity feed for repeated `Run Now` cycles.** A
  manual-rerun loop usually means a stuck phase that needs human
  attention rather than another session.

## What's next

- [Reading session journals](../../claude-harness/guides/reading-session-journals.md)
- [Working with baselines and artefact bundles](./working-with-baselines-and-artefact-bundles.md)
- [The Quality Gates view](./the-quality-view.md)
