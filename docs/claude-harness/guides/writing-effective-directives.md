# Steering the autonomous loop

The harness is the autonomous engine; Derive's Control Panel is your
steering surface. The operator's levers are not free-text
instructions — they're a small fixed set: a **STATUS** toggle, a
**MODE** toggle, a **WORKFLOW** dropdown, and three directive
**buttons** (Resume, Run Now, Force QC). This guide covers how to
combine them effectively.

> **Prerequisites:** ability to operate the loop on a project. In
> Derive, this is admin role. See
> [Driving the autonomous loop](../../derive/guides/driving-the-autonomous-loop.md)
> for the mechanics.

## The five operator levers

```
STATUS    : Active / Inactive          (is this project in the loop at all?)
MODE      : Auto / Manual              (scheduled vs operator-triggered?)
WORKFLOW  : <dropdown>                 (which pipeline runs?)
DIRECTIVE : Resume | Run Now | Force QC (what should the next session do?)
WORKFLOW  ← Back | Next →              (manual phase stepping)
```

That's the entire surface. There is no free-text "tell the loop what
to do next" field. Free-text guidance only enters the system through
**workflow config files** (file-based, dev work) — operators direct
behaviour by *choosing* among existing workflows and pressing the
right button.

## STATUS — is the project in the loop?

| Setting     | Behaviour                                              | Use when                                       |
| ----------- | ------------------------------------------------------ | ---------------------------------------------- |
| **Active**  | Harness scheduler considers this project.              | Most of the time — a project under active work. |
| **Inactive** | Harness ignores this project completely.              | Dormant projects, projects awaiting external input, post-baseline freeze. |

Setting STATUS to Inactive is the cleanest way to take a project out
of the loop without losing its data — flip it back to Active when
you're ready to resume.

## MODE — Auto vs Manual

| Setting    | Behaviour                                                  | Use when                                                            |
| ---------- | ---------------------------------------------------------- | ------------------------------------------------------------------- |
| **Auto**   | Sessions are scheduled automatically.                      | Steady-state autonomous operation.                                  |
| **Manual** | No scheduled sessions; sessions only run on **Run Now**.   | Reviewing output before more accumulates; cost-controlled work; debugging. |

Auto + Active is the normal state. Manual + Active is "I'll trigger
sessions myself." Inactive (regardless of MODE) means nothing
happens.

## WORKFLOW — which pipeline runs

The WORKFLOW dropdown picks which pre-defined pipeline runs in the
next session. Typical options on AIRGen projects:

| Workflow              | Purpose                                                                |
| --------------------- | ---------------------------------------------------------------------- |
| **Full Decomposition** | The end-to-end pipeline: concept → scaffold → decompose → QC → validate → review. Runs every phase in sequence. |
| **Tech Author**       | Polish wording for human audiences. No structural changes.             |
| **Validation**        | Validate substrate facts and trace coverage; surface drift.            |
| **Red Team**          | Adversarial review pass — find unmitigated paths and gaps.             |

The exact list depends on your harness config. Pick the workflow that
matches the work you want done next; the harness's state machine then
runs the phases in that workflow.

> **Switching workflows mid-stream is normal.** A typical project
> cycles: Full Decomposition for initial buildout → Validation when
> the structure stabilises → Tech Author when language is the focus
> → Red Team before a baseline → back to Validation if Red Team
> surfaces issues.

## The three directive buttons

Each button issues one of three state-machine signals to the harness.

### Resume

| Button     | Effect                                          |
| ---------- | ----------------------------------------------- |
| **Resume** | Lift any pause; let the scheduler run normally. |

Resume is the default. Use it after a pause when you're ready for
the loop to continue on its own.

### Run Now

| Button       | Effect                                                  |
| ------------ | ------------------------------------------------------- |
| **Run Now**  | Trigger a session immediately, ignoring the schedule.   |

Run Now is for operator-driven cadence. Use it when:

- You've just changed the WORKFLOW dropdown and want the new
  workflow to start now.
- You're in Manual mode and need a session.
- You're debugging and want a fresh session against the current
  state.

### Force QC

| Button       | Effect                                                          |
| ------------ | --------------------------------------------------------------- |
| **Force QC** | Force a Quality Check phase as the next session.                |

Force QC is the focused-quality lever. Use it when:

- A failing
  [Quality Gate](../../derive/guides/the-quality-view.md) needs
  attention before more decompose / scaffold work.
- You've manually fixed requirements and want the loop to re-run
  its quality checks against the corrected state.
- Output volume is fine but quality is sliding — Force QC
  prioritises polish over generation.

## Manual phase stepping

The visual state machine on the Control Panel has **← Back** and
**Next →** buttons. These let the operator manually move the
project's state-machine pointer between phases without running a
session — useful for:

- **Skipping a phase that doesn't apply** (e.g. a tool-qualification
  project that doesn't need a Red Team phase).
- **Re-running an earlier phase** after fixing an upstream issue.
- **Jumping to a specific phase** for targeted work.

Manual stepping mostly comes up when MODE is Manual; in Auto mode
the harness handles transitions itself.

## Combinations that work

A few canonical operator postures and the lever combinations that
produce them.

### "I want this project to run continuously."

```
STATUS   : Active
MODE     : Auto
WORKFLOW : Full Decomposition
```

The harness picks up sessions on its schedule and runs the full
pipeline.

### "I want to focus on quality before moving on."

```
STATUS   : Active
MODE     : Auto (or Manual)
WORKFLOW : (current)
DIRECTIVE: Force QC
```

The next session is a focused QC phase. After it lands, the gate
state on `/p/<slug>/quality` reflects the new state.

### "Stop everything; I need to review."

```
STATUS   : Inactive
```

Cleanest way to halt. Switch back to Active when you're done.

### "I'm reviewing — let me trigger sessions one at a time."

```
STATUS   : Active
MODE     : Manual
DIRECTIVE: Run Now (each time you want a session)
```

The harness only runs when you click Run Now.

### "Polish wording for the customer review."

```
STATUS   : Active
MODE     : Auto
WORKFLOW : Tech Author
```

The harness runs the Tech Author pipeline — no structural changes,
just language polish.

### "I think the substrate has drifted from AIRGen."

```
STATUS   : Active
MODE     : Auto
WORKFLOW : Validation
```

The validation workflow runs `facts reconcile` and similar checks,
surfacing drift in the journal post.

### "Pre-baseline final pass."

```
STATUS   : Active
MODE     : Manual
WORKFLOW : Red Team
DIRECTIVE: Run Now
```

A single adversarial-review session. Read the resulting journal
post, then move on to baselining.

## Antipatterns

A few patterns to avoid.

### Auto + Force QC repeatedly

Force QC stacks: each press queues another QC. If the underlying
problem is data-side (missing requirements, broken trace links),
running Force QC twenty times won't help. Fix the data, then re-run.

### Switching WORKFLOW without Run Now

Changing the dropdown alone doesn't trigger a session — the next
*scheduled* session uses it. If you want the change to take effect
immediately, follow up with Run Now.

### Leaving STATUS Active when the project's blocked

If a project needs human input that hasn't arrived, switch STATUS
to Inactive. Otherwise the harness keeps running sessions that
can't make progress, accumulating cost and journal noise.

### Skipping ahead via manual stepping without doing the phase

Pressing **Next →** moves the pointer; it doesn't do the work the
phase represents. If you skip Decompose without running it, the
project arrives at QC with an empty decomposition and the gate
fails.

## What replaces "free-text directives"

For free-text guidance, the path is **through Claude rather than
through Derive**. Wire the AIRGen, Reify, and UHT Substrate MCP
servers into Claude Desktop (see the
[MCP tour](../../ecosystem/mcp-tour.md)), then have Claude
manipulate the data directly:

> *"In project widget-x, polish the QA score on every requirement in
> document `system-requirements`. Don't change intent — only wording.
> Submit each as a candidate."*

Claude calls AIRGen's MCP tools to read, rewrite, and propose
candidates. The autonomous loop is for sustained, scheduled work;
Claude over MCP is for surgical, on-demand interventions.

## What's next

- [Understanding a harness session](./understanding-a-harness-session.md)
- [Reading session journals](./reading-session-journals.md)
- [Driving the autonomous loop (Derive)](../../derive/guides/driving-the-autonomous-loop.md)
- [The MCP tour: ten things to ask Claude](../../ecosystem/mcp-tour.md)
