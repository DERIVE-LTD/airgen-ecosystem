# Reading a Safety (SAF) diagram

The SAF diagram is Reify's view of safety analysis: hazards on the
left, the requirements that mitigate them in the middle, and the
verification activities that demonstrate the mitigation on the right.
It is the workspace's answer to "what could go wrong, and how do we
prove it doesn't?"

> **Prerequisites:** access to a Reify project with at least some
> hazards modelled (typically as substrate facts under
> `HAS_HAZARD`, with mitigating requirements in AIRGen).

## The three-column layout

Open `/p/<slug>/saf`. The layout is:

```
┌─────────────┐    ┌─────────────────────────┐    ┌────────────────┐
│   HAZARD    │    │    MITIGATING           │    │ VERIFICATION   │
│             │ ─▶ │    REQUIREMENTS         │ ─▶ │  ACTIVITIES    │
└─────────────┘    └─────────────────────────┘    └────────────────┘
```

Each hazard sits as a node on the left. The trace links from the
mitigating requirements (`satisfies` or `mitigates` depending on
your project's vocabulary) connect into the centre column. The
verification activities sit on the right, linked to mitigating
requirements by `verifies`.

## Node symbology

### Hazards

Hazard nodes carry:

- **Identifier** — the hazard's reference (e.g. `HAZ-003`).
- **Description** — short statement of the failure mode.
- **Severity / SIL** — when present, displayed as a coloured band
  (e.g. SIL-3, ASIL-D, DAL-A — depending on the standard).
- **Cause and effect** — when modelled, surfaced in the hover panel.

### Mitigating requirements

These are the same nodes as on the REQ diagram (same colour scheme,
same symbology) — they show up here because they have an outgoing
trace to a hazard.

### Verification activities

Same as on the REQ diagram. A green pill means passed evidence is
attached.

## Per-hazard tabs

The SAF view has **one tab per hazard** by default. This keeps the
diagram readable on projects with twenty hazards each carrying half
a dozen mitigating requirements — you wouldn't want them all on
screen at once.

Switch tabs to focus on a single hazard's full chain:

- The hazard node
- All requirements that mitigate it
- All verification activities for those requirements
- Any unmet links (mitigation declared but no requirement, or
  requirement with no verification)

## Reading patterns

### 1. "Is this hazard covered?"

For a given hazard, you want at least one mitigating requirement,
and each mitigating requirement should have at least one
verification activity. The tab view is calibrated for this question.

A clean hazard tab looks like:

```
HAZ-003 ──▶ SYS-014 ──▶ Activity-T-007 (passed)
       └──▶ SYS-015 ──▶ Activity-A-002 (passed)
```

A problematic hazard tab looks like:

```
HAZ-003 ──▶ SYS-014 ──▶ Activity-T-007 (in progress)
       └──▶ ?      (no requirement)
```

The "no requirement" gap means the hazard is partially declared but
not yet mitigated — a clear action item.

### 2. "Is the SIL allocation right?"

Hover over a mitigating requirement and look at its claimed SIL
versus the hazard's required SIL. If the requirement claims SIL-2
but the hazard requires SIL-3, the allocation isn't right. (Reify
shows the SIL where it's modelled in substrate; if your project
doesn't model SIL, this question doesn't apply.)

### 3. "What evidence proves this hazard is mitigated?"

Click into a verification activity. The hover or detail panel shows
the evidence records attached. Look for:

- At least one passed evidence record.
- Recent enough — evidence stale by years on a regulated project is
  typically a red flag.
- Records signed by an appropriate role.

### 4. Cross-check with substrate

The SAF view is a Reify rendering of facts that live in UHT
Substrate. To audit the source data:

```sh
uht-substrate facts query \
  --namespace SE:widget-x \
  --predicate HAS_HAZARD
```

The substrate query should match the hazards you see in the diagram.
A mismatch means substrate / Reify drift — see
[Reconciling substrate facts against AIRGen trace links](../../uht-substrate/guides/reconciling-substrate-facts-against-airgen-trace-links.md).

## Layout choices

The default ELK layered layout is best for SAF: it produces a clean
left-to-right flow. Force-directed and tree layouts exist but rarely
help — the columnar story is what you want.

## A typical safety review workflow

1. Open the SAF view.
2. Walk every hazard tab in order. For each:
   - Confirm at least one mitigating requirement.
   - Confirm at least one verification activity per mitigating
     requirement, with passed evidence.
   - Note any gaps in a review log.
3. Cross-check the hazard list against substrate (`facts query
   --predicate HAS_HAZARD`).
4. Generate a baseline (`airgen bl create`) once the review is clean.
5. Export the safety baseline as part of the regulatory deliverable.

## What's next

- [Reading a Requirements (REQ) diagram](./reading-a-requirements-req-diagram.md)
- [Working with baselines and artefact bundles (Derive)](../../derive/guides/working-with-baselines-and-artefact-bundles.md)
- [Reconciling substrate facts against AIRGen trace links](../../uht-substrate/guides/reconciling-substrate-facts-against-airgen-trace-links.md)
