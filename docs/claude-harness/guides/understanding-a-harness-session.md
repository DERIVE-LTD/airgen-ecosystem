# Understanding a harness session: phases, gates, and outputs

A harness session is one Claude-driven traversal of a state machine
that produces useful work for a project. Sessions are where the
"autonomous" part of the AIRGen autonomous loop actually happens.
Understanding what a session is — and isn't — is the foundation for
operating the loop effectively from Derive.

This guide walks through the session lifecycle, the phases in the
multi-template pipeline, the gates that decide whether output gets
committed, and the side effects you should expect.

> The harness source is private. This guide describes the
> operator-facing model — what you see and act on through Derive —
> not the internal implementation.

## The session in one paragraph

A session is bounded work: it has a start (driven by a directive or
a schedule), runs as a state machine through a pre-defined workflow
(concept, scaffold, decompose, QC, validate, review, …), produces
candidates and facts and a journal post, and ends. Each session is
independent — the harness doesn't carry hidden state between sessions
beyond what's visible in AIRGen, UHT Substrate, and the journal.

## The pipeline phases

A typical AIRGen workflow exposes these phases:

| Phase       | Purpose                                                  |
| ----------- | -------------------------------------------------------- |
| **concept** | Establish the system concept and the stakeholder needs.   |
| **scaffold**| Build the initial document and section structure.        |
| **decompose** | Break stakeholder needs into system / sub-system requirements. |
| **MBSE expert** | Apply MBSE-pattern judgement (interfaces, modes, structure). |
| **QC**      | Apply QA / EARS / domain checks.                          |
| **validate**| Cross-check against substrate facts and trace coverage.   |
| **review**  | Adversarial / red-team pass over the result.              |
| **technical author** | Polish wording for human audiences.              |

Not every project runs every phase. A research project might run
only `decompose` and `QC`; a regulated project will run the full
pipeline including `validate` and `review`.

The session you see in Derive's loop control panel is one of these
phases. Successive sessions cycle through phases on a schedule, with
operator directives shifting focus.

## What a session reads

Before producing output, a session reads:

- **AIRGen.** The current requirements, trace links, baselines, and
  documents for the project.
- **UHT Substrate.** All facts and entities under
  `SE:<project-slug>`.
- **Journal.** Recent session posts, so the new session has context
  on what happened recently.
- **Directives.** The current operator directives.
- **Workflow config.** The phase-specific prompt template, gates,
  and turn budget.

This is why directives are powerful: they are part of the input
context for every subsequent session.

## What a session writes

The session's outputs are visible in three stores:

- **AIRGen** — new candidate requirements, trace links, baselines
  (rarely; usually a human creates baselines).
- **UHT Substrate** — facts under the project namespace.
- **Journal** — a markdown post describing what was done, why, and
  any anomalies encountered.

All AIRGen writes go in as **candidates** — pending human review.
This is the human-in-the-loop gate that keeps the platform
trustworthy in regulated contexts. Substrate facts are upserted
directly (no candidate gate); they are easier to revoke than
requirements.

## The gates

Each phase has gates that decide whether a session's output gets
committed:

- **Parser gate.** Does the SysML the session wrote actually parse?
- **Schema gate.** Do the substrate facts match the predicate
  conventions the project expects?
- **Trace gate.** Does every new requirement have at least one
  proposed trace link?
- **QA gate.** Does the average QA score of new requirements meet a
  threshold (configurable per phase)?
- **Validation gate.** For the `validate` phase, do the
  cross-references hold up?

A failed gate causes the session to:

- Write its journal post anyway (so you can see what failed).
- *Not* commit AIRGen candidates (or commit them with a "blocked"
  status).
- *Not* upsert substrate facts.

Failed-gate sessions are visible in Derive as red rows in the
session log.

## Session length and turn budget

Sessions have a turn budget — typically 10–30 Claude turns for an
enrichment session, more for a complex decompose run. The harness
runs Claude with a specific output format (`--output-format json`)
to ensure structured outputs even when most turns are tool calls.

If a session hits its turn budget without finishing, it ends and
writes a journal post explaining what was incomplete. Operators can
either:

- Pick a more focused workflow from the WORKFLOW dropdown and
  click **Run Now** so the next session continues against the
  current state, or
- Increase the turn budget for that phase in the workflow config.

For surgical, free-text-style interventions ("polish QA on the
requirements produced in session X"), use the AIRGen MCP server
from Claude Desktop directly — see
[the MCP tour](../../ecosystem/mcp-tour.md). The harness's job is
the sustained autonomous pipeline; Claude over MCP handles
operator-specific asks.

## Cost per session

Each session costs Claude API tokens. Approximate ranges:

- Small enrichment: $0.50 – $2.
- Medium decomposition: $2 – $5.
- Large decomposition / red-team: $5 – $15.
- Failed-and-retried: cost of the failure plus the retry.

These figures vary with model choice, project size, and turn budget.
Watch the Claude API console for the actual numbers.

## When to start a session manually

The loop runs on a schedule, but you can trigger a session
manually for:

- **Onboarding.** First session on a new project, to check the
  workflow config is right.
- **After a directive.** You want the new directive applied
  immediately.
- **Testing.** After a workflow config change.

In Derive, the per-project control panel exposes a "run now" action
when permitted. Otherwise, wait for the next scheduled session — the
loop default is reasonably frequent (every few minutes for active
projects, hourly for slow ones, depending on your install).

## What the harness will never do

Some things are explicitly out of scope for sessions:

- **Commit a baseline.** Operators decide when to baseline.
- **Approve candidates.** The candidate-review gate is a human
  responsibility.
- **Modify project settings.** Permissions, regimes, and workflow
  configs are operator-only.
- **Touch other projects.** Sessions are scoped to one project's
  data.
- **Run shell commands.** The harness has no host-side tool access
  beyond its API clients.

Treat the harness as a focused worker, not a free agent.

## What's next

- [Steering the autonomous loop](./writing-effective-directives.md)
- [Reading session journals](./reading-session-journals.md)
- [The relationship between harness sessions, AIRGen baselines, and substrate facts](./relationship-between-sessions-baselines-and-substrate-facts.md)
- [Driving the autonomous loop (Derive)](../../derive/guides/driving-the-autonomous-loop.md)
