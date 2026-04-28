# The relationship between harness sessions, AIRGen baselines, and substrate facts

The autonomous loop touches three stores — AIRGen, UHT Substrate,
and the journal filesystem. Baselines, candidates, and facts each
have their own lifecycle, their own audit trail, and their own
operator workflow. Understanding how the three relate is the
difference between a project that stays coherent and one that drifts.

This guide is the mental model. If you've read the other harness
guides, this is the synthesis.

> **Audience:** operators who already understand sessions,
> directives, and journals. If you don't, read those guides first.

## The four artefacts

Every harness session may produce up to four kinds of artefact:

| Artefact            | Lives in                         | How it's reviewed                      |
| ------------------- | -------------------------------- | -------------------------------------- |
| **Candidate requirement** | AIRGen                     | Operator accepts / rejects / edits.    |
| **Trace link**            | AIRGen (often as candidates) | Operator accepts / rejects.            |
| **Substrate fact**        | UHT Substrate              | Auto-applied; operator can revoke.     |
| **Journal post**          | Filesystem (markdown)      | Operator reads; immutable.             |

The asymmetry is intentional:

- **AIRGen content** carries semantic weight (it's the system of
  record). Candidates need explicit human review before they enter
  the live data set.
- **Substrate facts** are easier to undo (`facts delete` or
  `entities merge`). The loop writes them directly.
- **Journal posts** are pure record — append-only audit trail.

## The flow per session

```
Session starts
    │
    ├─▶ Reads context from AIRGen, Substrate, journal, directives
    │
    ├─▶ Runs Claude conversation through the workflow's state machine
    │
    ├─▶ Writes substrate facts (upsert, immediate)
    │
    ├─▶ Writes AIRGen candidates (gated; pending operator review)
    │
    ├─▶ Writes journal post (immediate, immutable)
    │
    └─▶ Session ends
```

Note the ordering: substrate facts are written first (most often),
then AIRGen candidates, then the journal. If a session fails after
substrate writes but before AIRGen writes, you can have substrate
facts that no requirement references — pick this up via
`facts reconcile`.

## Where baselines fit

Baselines are **operator-driven**, not session-driven. The harness
never creates a baseline — only operators do. This is the boundary:

- The loop produces *content* — requirements, traces, facts, posts.
- The operator decides when that content is *baselined* — frozen as
  a release-gate snapshot.

A baseline captures:

- Every AIRGen requirement at its current state and version.
- Every active trace link.
- The verification matrix.
- The canonical SysML source.
- Substrate facts under the project namespace.

A baseline does **not** capture:

- The harness session history (that lives in the journal).
- Pending candidates (only accepted ones).
- Operator directives.

This means a baseline is a **content snapshot**, not a **process
snapshot**. To audit the process, walk the journal.

## A complete project audit trail

For a regulator or auditor asking "what happened to this project",
the answer combines all three stores:

1. **The journal** — chronological narrative of every session.
2. **AIRGen audit log** — version history of every requirement, who
   accepted each candidate, who created each baseline.
3. **Substrate fact history** — entity classification history
   (`entities history`), fact creation timestamps.

Together they tell the whole story:

> *"On 2026-04-15, the loop ran a `decompose` session against
> stakeholder need STK-007 (journal post). It produced 8 candidate
> system requirements (AIRGen log). 7 were accepted by S. Holland
> on 2026-04-16, 1 was rejected (AIRGen log). The 7 accepted
> requirements were baselined in `v1.0` on 2026-04-20 (AIRGen
> baseline). The substrate facts that support the SysML reconstruction
> were written by the same session (substrate fact creation
> timestamps)."*

## Common drift modes

Drift happens when the three stores get out of sync. The most
common cases:

### Substrate fact references a deleted requirement

A session writes a fact like *"REQ-014 SATISFIES HAZ-003"*. Later
REQ-014 is deleted in AIRGen. The substrate fact now references a
ghost.

- **Detect:** `uht-substrate facts reconcile`.
- **Resolve:** Either delete the fact or restore the requirement.

### Trace link references a candidate that was never accepted

The loop proposed both a requirement and a trace link. The
requirement was rejected; the trace link refers to it.

- **Detect:** AIRGen orphan report.
- **Resolve:** Reject the trace link too.

### Baseline references state that's been "fixed" since

Baselines are immutable. A baseline taken on 2026-04-15 reflects
the state on 2026-04-15, even if you've since improved
requirements.

- **Not actually drift** — this is the point of baselines. But
  reviewers sometimes confuse "the baseline" with "current state".
  Make sure release notes are clear about which state is being
  reviewed.

### Substrate fact predicate inconsistent with AIRGen trace link type

Substrate uses `MITIGATED_BY`, AIRGen uses `satisfies`. They could
both be right, but a downstream consumer (Reify) might struggle.

- **Detect:** `facts reconcile` reports predicate mismatches.
- **Resolve:** Pick a canonical name, update the other side.

## Operator's mental model

The cleanest mental model:

- **The loop produces.** Sessions write candidates and facts and
  posts.
- **The operator approves.** Walking the candidates is the
  human-in-the-loop gate.
- **Substrate is graph state.** Cheap to add, cheap to revoke.
- **AIRGen is record.** Once accepted, it's the project's truth.
- **Baselines are commitments.** Frozen records of what was
  promised at a moment in time.
- **The journal is audit.** Append-only narrative of how we got
  here.

If you keep these distinct in your head, the system stays coherent.

## A weekly review pattern

A pattern that keeps drift small:

1. **Monday.** Read the cross-project journal. Note any failed
   sessions.
2. **Tuesday.** Walk pending candidates per project. Accept, reject,
   edit.
3. **Wednesday.** Run `facts reconcile` on each active project.
   Address findings.
4. **Thursday.** Quality view. Resolve orphans, lift QA scores.
5. **Friday.** If any project hit a release-gate state, baseline.
   Generate artefacts for delivery.

A team of two or three operators can run this rhythm across 20–30
projects without drama.

## What's next

- [Understanding a harness session](./understanding-a-harness-session.md)
- [Steering the autonomous loop](./writing-effective-directives.md)
- [Reading session journals](./reading-session-journals.md)
- [Working with baselines and artefact bundles (Derive)](../../derive/guides/working-with-baselines-and-artefact-bundles.md)
- [Reconciling substrate facts against AIRGen trace links (UHT Substrate)](../../uht-substrate/guides/reconciling-substrate-facts-against-airgen-trace-links.md)
