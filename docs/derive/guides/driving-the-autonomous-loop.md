# Driving the autonomous loop: pause, unpause, and directives

Derive's loop control panels are how operators interact with the
[Claude Harness](../../claude-harness/) — the autonomous engine that
runs the systems-engineering pipeline. The harness has no UI of its
own; Derive's `/control` (global) and `/p/<slug>/control` (per-
project) panels are the only operator interface.

This guide explains the loop's lifecycle, the three operator
controls (pause, unpause, direct), and the patterns that produce
high-quality output.

> **Prerequisites:** an account on a Derive instance with permission
> to control the loop on at least one project. Loop-control rights
> are typically restricted to senior engineers or admins.

## The loop in one paragraph

For each project, the harness can run a continuous loop of Claude-
driven sessions. A session is one phase of the pipeline (concept,
scaffold, decompose, QC, validate, review). Sessions read existing
context from AIRGen and UHT Substrate, run a Claude conversation
using a config-driven workflow, and write candidates back to AIRGen
plus facts to the substrate. Each session also produces a markdown
journal post that Derive renders.

When the loop is running, sessions are scheduled and executed
without per-turn human prompts. The operator's job is not to drive
each session — it's to **frame** the loop's work, **pause** when
something looks wrong, and **review** what gets produced.

## The two control panels

### `/control` — global

The global panel shows every project the loop is running against,
with a row per project:

| Project        | Status     | Last session    | Pending review |
| -------------- | ---------- | --------------- | -------------- |
| widget-x       | running    | 14m ago, ok     | 3 candidates   |
| thermal-bench  | paused     | 2d ago, failed  | 0              |
| beta-rocket    | running    | 6m ago, ok      | 12 candidates  |

Pause / unpause buttons act on each row. Use the global panel for
managing many projects at once — paused while a release ships,
unpaused otherwise.

### `/p/<slug>/control` — per-project

Drill into a single project. The per-project panel adds:

- **Directive history** — every directive submitted, with the loop's
  acknowledgement.
- **Session log** — recent sessions with phase, duration, outcome.
- **Quality state** — current gate state and trends.
- **Submit directive** — a free-text input for the next directive.

This is where you'll spend most of your loop-control time.

## Pause and unpause

Pausing stops the loop from scheduling new sessions. In-flight
sessions continue to completion (they can't be killed mid-turn
without losing context).

Reasons to pause:

- **Reviewing candidates.** You want to inspect what was produced
  before more sessions stack on top.
- **Pre-baseline freeze.** No more changes before the release-gate
  baseline.
- **Diagnostic.** A session ended in failure and you want to
  investigate before the loop tries again.
- **Cost control.** The loop costs Claude API tokens on every
  session; pause when the project is dormant.

Unpause when you're ready for the loop to schedule the next session.

## Directives

A directive is a free-text instruction the loop incorporates into
its next session's context. Examples:

- *"Focus on hazard analysis next. Use the SIL-2 hazard worksheet
  format."*
- *"Resolve orphan trace links in `system-requirements`. For each,
  propose a parent and a verification activity."*
- *"Stop creating new requirements. Polish QA scores on existing
  requirements until median ≥ 80."*
- *"The thermal subsystem decomposition is wrong — the radiator
  doesn't connect to the loop. Re-run decomposition with that
  context."*

Directives are stored on the project; the harness reads them when
starting a session and includes them in the prompt context. Multiple
directives stack — the most recent take precedence.

To submit a directive:

- **UI** — type into the directive box on `/p/<slug>/control`,
  click submit. The loop acknowledges; the directive shows in the
  history.
- **CLI** — use the AIRGen MCP server's directive tool from Claude
  (or the Derive UI; there isn't a separate CLI for directives in
  every install).

For more on writing effective directives, see
[Writing effective directives for the autonomous loop](../../claude-harness/guides/writing-effective-directives.md).

## A working session pattern

A productive working session with the loop:

1. **Open `/p/<slug>/control` and `/p/<slug>/journal` side by side.**
2. **Read the most recent journal post.** Understand what the loop
   just did and why. If anything looks confused, draft a directive
   to clarify.
3. **Pause.** Stops new sessions while you review.
4. **Walk the candidates.** In AIRGen (`airgen ai candidates`),
   accept the good ones, reject the bad ones, edit the
   nearly-right ones.
5. **Submit a directive** that names the next focus.
6. **Unpause.** The next session picks up the directive.
7. **Move on.** Come back when the loop has produced more output.

This is operator work — not babysitting. A useful tempo is
"twice-daily review" for active projects, "weekly review" for slow
projects.

## Loop failure modes

Sessions sometimes fail. The journal post will tell you why. Common
failures:

- **Timeout.** A session ran too long (default ~5 minutes for a
  30-turn enrichment session). Either raise the timeout in the
  workflow config or split the work into smaller phases.
- **Parser error.** A session's SysML output didn't parse. Look at
  the dry-run output in the journal post.
- **Validation gate.** A session failed a pre-commit check (orphan
  facts, schema mismatch). Same investigation.
- **API error.** Claude API rate limit, network, etc. The loop
  should retry these automatically.

When in doubt, pause and read the journal post end-to-end. The
harness is verbose by design.

## Cost management

Each session costs Claude API tokens. Rough order-of-magnitude:

- A small enrichment session: $0.50 – $2.
- A large decomposition / red-team session: $5 – $15.
- A failed-and-retried session: cost of the failure plus the retry.

Strategies:

- **Pause dormant projects.** Don't pay for sessions on a project
  no one is reviewing.
- **Use directives to scope.** A scoped directive ("polish QA on
  existing reqs") produces shorter sessions than an open-ended one
  ("improve the project").
- **Watch for retry loops.** A session that fails repeatedly is
  burning tokens; pause and investigate.

## What's next

- [Writing effective directives for the autonomous loop](../../claude-harness/guides/writing-effective-directives.md)
- [Reading session journals](../../claude-harness/guides/reading-session-journals.md)
- [Working with baselines and artefact bundles](./working-with-baselines-and-artefact-bundles.md)
