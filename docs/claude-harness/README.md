# Claude Harness

**Autonomous Claude Code session harness — the engine that runs the
AIRGen ecosystem's autonomous systems-engineering pipeline.**

The Claude Harness is the orchestration layer behind [Derive](../derive/).
It runs Claude-driven sessions through a config-driven state machine,
executes multi-template workflows (concept → scaffold → decompose → QC
→ validate → review), and writes the resulting requirements, trace
links, and substrate facts back to AIRGen and UHT Substrate. Each
session also produces a journal post that Derive renders for human
review.

> **Source:** the harness lives in a private repository. This page
> documents its public-facing role within the AIRGen ecosystem; for
> direct access, contact [Derive Ltd](https://derive-ltd.co.uk).

## What the harness does

| Capability                   | Summary                                                                       |
| ---------------------------- | ----------------------------------------------------------------------------- |
| Autonomous loop              | Runs end-to-end systems-engineering sessions without human prompts per turn.  |
| Config-driven workflows      | Workflow definitions (concept, decompose, QC, validate, review, …) are config, not code. |
| State machine                | Each session is a state machine — well-defined transitions, retries, and gates. |
| Writes upstream              | Produces AIRGen requirements + trace links and UHT Substrate facts per session. |
| Journal output               | Each session writes a markdown post that Derive renders.                      |
| Driven from Derive           | Derive's `/control` and `/p/<slug>/control` panels pause, unpause, and direct the loop. |

## Role in the ecosystem

```
┌──────────────────┐    AIRGen API      ┌──────────────┐
│  Claude Harness  │ ─────────────────▶ │    AIRGen    │
│  (autonomous)    │  reqs + trace links│              │
└──────┬───────────┘                    └──────────────┘
       │
       │ facts (SE:<slug>)            ┌────────────────┐
       └────────────────────────────▶ │ UHT Substrate  │
       │                              └────────────────┘
       │
       │ markdown                      ┌────────────────┐
       └─────────────────────────────▶ │   Filesystem   │ ──▶ Derive journal
                                       │   (journal)    │
                                       └────────────────┘
```

Where Derive is the operator workbench, the harness is the engine.
Where Reify is the SysML view of the result, the harness is what
produced the result.

## Guides

Start here:

- [Getting started](./getting-started.md) — how to engage with the
  harness via Derive

In-depth guides:

- [Understanding a harness session: phases, gates, and outputs](./guides/understanding-a-harness-session.md)
- [Steering the autonomous loop (STATUS, MODE, WORKFLOW, directives)](./guides/writing-effective-directives.md)
- [Reading session journals](./guides/reading-session-journals.md)
- [The relationship between harness sessions, AIRGen baselines, and substrate facts](./guides/relationship-between-sessions-baselines-and-substrate-facts.md)
