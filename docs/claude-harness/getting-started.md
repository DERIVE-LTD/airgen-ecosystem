# Getting started with the Claude Harness

The Claude Harness is the autonomous engine behind [Derive](../derive/).
For most users the harness is invisible — you interact with it through
Derive's loop controls and read its output through Derive's journal.
This guide explains the operator's view.

> The harness source is in a private repository. This guide focuses on
> how to engage with the harness from Derive. If you need direct
> access, contact [Derive Ltd](https://derive-ltd.co.uk).

## 1. The autonomous loop

For each project, the harness can run a continuous loop of Claude-driven
sessions. A session is one state-machine traversal — typically a single
phase of the pipeline (e.g. concept, decompose, QC, validate, review).
Sessions write their results to AIRGen (requirements, trace links) and
UHT Substrate (facts), and produce a journal markdown post.

When the loop is unpaused, sessions are scheduled and executed without
per-turn human prompts.

## 2. Pause, unpause, and direct the loop

From Derive, the loop is controlled by:

- **`/control`** — global controls across all projects
- **`/p/<slug>/control`** — controls scoped to one project

Use these to pause the loop while you review output, submit a
**directive** (a free-text instruction the loop will incorporate), or
unpause when you are happy.

## 3. Read the journal

Each session writes a markdown post. Derive renders the journal at:

- `/journal` — cross-project feed
- `/p/<slug>/journal` — per-project feed
- `/p/<slug>/journal/<entry>` — single post

The journal is the operator-readable narrative of what the loop did
and why.

## 4. Inspect what the loop produced

The session will have changed:

- **AIRGen** — new or updated requirements and trace links
- **UHT Substrate** — new facts under the project's namespace
- **Derive** — the dashboard, spec-tree, and quality view will
  reflect the new state

Use Derive's quality view (`/p/<slug>/quality`) to see whether the
session output passes the current quality gate.

## 5. Iterate

Common operator workflow:

1. Read the latest journal post.
2. Check the quality view — orphans, lint failures, gate state.
3. Either accept the output (move on, baseline) or write a directive
   to address gaps.
4. Unpause the loop.

## Next steps

- _TODO: link to "Understanding a harness session: phases, gates, and outputs"_
- _TODO: link to "Writing effective directives for the autonomous loop"_
- _TODO: link to "The relationship between harness sessions, AIRGen baselines, and substrate facts"_
