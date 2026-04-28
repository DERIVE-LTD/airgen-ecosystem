# Reading a Safety (SAF) diagram

The SAF view at `/p/<slug>/saf` is Reify's per-hazard safety
inspector. Pick a hazard from the sidebar and the canvas renders
the hazard, the requirements that mitigate it, and the
verification activities that demonstrate the mitigation — for
that one hazard.

This guide walks the layout, the symbology, and the reading
patterns this view is good for.

> **Prerequisites:** access to a Reify project with at least some
> hazards modelled (typically as substrate facts under
> `HAS_HAZARD`, with mitigating requirements in AIRGen).

## Layout

The SAF view is two panes:

- **Left sidebar** — every hazard in the project, listed by
  reference (e.g. `H-001 Hazard Analysis` through
  `H-007 Hazard Analysis` in the demo).
- **Canvas** — the hazard's local subgraph: hazard node →
  mitigating requirement nodes → verification activity nodes.
  Picking a hazard updates the URL to
  `/p/<slug>/saf?view=view-saf-<ref>`.

Above the canvas is a stats strip
(e.g. `1 hazards, 10 SIL requirements, 6 mitigation links,
4 verification links`) and the section header **Safety Diagram**.
The same canvas controls as the other diagram views.

## Node symbology

### Hazards

A hazard node carries:

- **Identifier** — the hazard's reference (e.g. `H-003`).
- **Description** — short statement of the failure mode.
- **Severity / SIL** — when present, displayed alongside the
  hazard descriptor (e.g. SIL-3, ASIL-D, DAL-A — depending on
  the standard your project uses).

### Mitigating requirements

These are the same nodes you'd see on the REQ diagram (same
colour scheme, same kinds), but here they show up because
they're linked from a hazard.

### Verification activities

Verification cases for the mitigating requirements. Hover for
metadata; click to focus.

## Edge types

| Type        | Reading                                                      |
| ----------- | ------------------------------------------------------------ |
| `mitigates` | Requirement mitigates the hazard.                            |
| `verifies`  | Verification activity verifies the mitigating requirement.   |

The stats strip names these explicitly: "N mitigation links,
N verification links" for the focused hazard.

## Reading patterns

### 1. "Is this hazard covered?"

Pick the hazard from the sidebar. You want to see:

- At least one mitigating requirement (`mitigates` edge).
- Each mitigating requirement followed by at least one
  verification activity (`verifies` edge).

A clean hazard subgraph looks like:

```
H-001 ──mitigates──▶ SYS-REQ-014 ──verifies──▶ Activity-T-007
      └─mitigates──▶ SYS-REQ-015 ──verifies──▶ Activity-A-002
```

If the chain breaks anywhere — a hazard with no mitigations, or
a mitigation with no verification — that's an action item.

### 2. "Is the SIL allocation right?"

Hover over a mitigating requirement and look at its claimed SIL
versus the hazard's required SIL. If the requirement claims SIL-2
but the hazard requires SIL-3, the allocation isn't right.
(Reify shows the SIL where it's modelled in substrate; if your
project doesn't model SIL, this question doesn't apply.)

### 3. Walk every hazard

The sidebar makes a checklist: for each hazard, focus the canvas,
walk the chain, note any gaps. The list is short enough that
this is a useful pre-baseline ritual on regulated projects.

### 4. Cross-check with substrate

The SAF view is a Reify rendering of facts that live in UHT
Substrate. To audit the source data:

```sh
uht-substrate facts query \
  --namespace SE:<slug> \
  --predicate HAS_HAZARD
```

The substrate query should match the hazards in the sidebar. A
mismatch means substrate / Reify drift — see
[Reconciling substrate facts against AIRGen trace links](../../uht-substrate/guides/reconciling-substrate-facts-against-airgen-trace-links.md).

## Saved views

The **`+ Create View`** affordance at the top of the sidebar lets
you save the current focus as a named view. Useful for hazards
that get reviewed often.

## A typical safety review workflow

1. Open `/p/<slug>/saf`.
2. Walk every hazard in the sidebar in order. For each:
   - Confirm at least one mitigating requirement.
   - Confirm at least one verification activity per mitigating
     requirement.
   - Note any gaps in a review log.
3. Cross-check the hazard list against substrate
   (`facts query --predicate HAS_HAZARD`).
4. Export the safety documentation from Derive's
   [Downloads tab](../../derive/guides/exports-and-document-bundles.md)
   (Verification Plan + Design Description) once the review is
   clean.

## What's next

- [Reading a Requirements (REQ) diagram](./reading-a-requirements-req-diagram.md)
- [Exports and document bundles (Derive)](../../derive/guides/exports-and-document-bundles.md)
- [Reconciling substrate facts against AIRGen trace links](../../uht-substrate/guides/reconciling-substrate-facts-against-airgen-trace-links.md)
