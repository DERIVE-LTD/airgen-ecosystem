# The quality view: lint findings, orphans, gate state

The quality view at `/p/<slug>/quality` is where Derive answers the
question "is this project ready to ship the next deliverable?". It
aggregates AIRGen lint results, orphan trace links, ambiguity
detections, and the current quality gate state into one screen.

This guide walks through the panels, explains the gate model, and
suggests a working pattern for reaching a clean view.

> **Prerequisites:** a Derive project with at least some
> requirements. The view assumes the AIRGen lint engine has run
> recently; trigger a fresh run from the
> [CLI](../../airgen/guides/using-the-airgen-cli.md) if needed.

## Panels at a glance

The quality view has four panels:

1. **Gate state.** Pass / fail / blocked, with the conditions.
2. **Lint findings.** Output of `airgen lint` plus regime-aware
   safety rules.
3. **Orphans.** Requirements with no incoming or outgoing trace
   links.
4. **Ambiguous requirements.** Requirements where the QA engine
   couldn't classify the EARS pattern, or detected ambiguous
   wording.

## The gate

The quality gate is a binary "ready or not" verdict, computed from a
project-configurable rule set. A typical gate definition for a
SIL-rated project:

```
Gate: SIL-2 ready
─────────────────
✓  All requirements have a QA score ≥ 70
✓  No orphan requirements
✗  All hazards have at least one mitigating requirement
✓  All mitigating requirements have at least one verification activity
✗  All verification activities have passed evidence
```

The two failing rows would block the gate. Fix them, re-run, and
the gate goes green.

Different regimes have different gates. A research project might
have a much looser gate (just QA score). A DO-178C DAL-A project
might have a much tighter one (every requirement traced to a
software architectural element with passing evidence).

## Lint findings

`airgen lint` classifies every domain concept in your requirements
through UHT Substrate and surfaces ontological mismatches:

- **Ontological mismatch.** A requirement attaches a physical
  property (e.g. weight) to a non-physical entity (e.g. a state).
- **Missing statistical parameters.** An abstract metric (mean time
  between failures, error rate) given without a confidence
  interval.
- **Mode-without-criteria.** A degraded operating mode declared
  without performance criteria.
- **Verification mixed with functional.** A requirement that is
  partly behaviour, partly verification.
- **Ontological ambiguity.** Two concepts in the same requirement
  that disagree on classification.
- **Missing modal verb.** Requirement without "shall".

Each finding has a one-line summary and a suggestion. Click for the
context — which requirements are affected, what the suggested
rewording is.

The findings are **regime-aware**. A tool-qualification project
doesn't get SIL warnings; a research project doesn't get DO-178C
warnings.

## Orphans

A requirement is an orphan if it has no incoming or no outgoing
trace links. The orphan panel groups them:

- **No parent.** Requirement isn't derived from anything (often a
  stakeholder need that should be linked, or a derived requirement
  that lost its parent in a refactor).
- **No child.** Requirement isn't refined further (often a bottom-of-
  hierarchy node, but sometimes a missing decomposition).
- **No verification.** Requirement has no `verifies` link from a
  verification activity.

Each entry includes the requirement reference, document, and a
quick-action button to add the missing link.

## Ambiguous requirements

These are requirements the QA engine flagged as:

- **EARS pattern ambiguous.** Multiple plausible classifications
  (could be event-driven or state-driven).
- **Trigger / response unclear.** The sentence structure doesn't
  cleanly separate the two.
- **Quantification missing.** Numeric thresholds without units or
  measurement window.

Treat these as a refactor backlog. Each fix improves the QA score
and the diagram clarity in Reify (the REQ diagram colour-codes by
QA bucket — fewer ambiguous nodes makes for a calmer diagram).

## A working pattern

A clean quality view doesn't happen by accident. The pattern that
works:

1. **Run `airgen lint` weekly.** It's cheap. Catch ontological
   issues while they're small.
2. **Walk the orphan list before reviews.** Resolve each entry —
   either link, mark as intentionally untraced, or delete.
3. **Sort ambiguous requirements by QA score.** The lowest-scoring
   ambiguous ones give the biggest score lift per minute of effort.
4. **Aim for green gate before baseline.** The gate is what stops a
   weak deliverable shipping. If you baseline with the gate red, the
   baseline carries the failure forward.

## Driving the loop from the quality view

If the quality view shows lots of fixable issues — orphans, low QA
scores — the autonomous loop can probably help. Submit a directive
to the loop (see [Driving the autonomous loop](./driving-the-autonomous-loop.md))
like:

> "Resolve orphan trace links in document `system-requirements`. For
> each, propose a parent requirement in `stakeholder-requirements`
> and a verification activity. Submit candidates."

The loop will produce candidates — you accept or reject them in
AIRGen, and the quality view updates.

## What's next

- [Reading the per-project dashboard](./reading-the-per-project-dashboard.md)
- [Driving the autonomous loop](./driving-the-autonomous-loop.md)
- [Working with baselines and artefact bundles](./working-with-baselines-and-artefact-bundles.md)
- [Building a traceability matrix (AIRGen)](../../airgen/guides/building-a-traceability-matrix.md)
