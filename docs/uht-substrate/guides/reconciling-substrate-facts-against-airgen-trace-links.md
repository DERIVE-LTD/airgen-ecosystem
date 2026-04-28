# Reconciling substrate facts against AIRGen trace links

Two stores hold related but independent state:

- **AIRGen** owns requirements and typed trace links between them.
- **UHT Substrate** owns entities and facts (`PART_OF`, `HAS_HAZARD`,
  …) about the same concepts.

When the autonomous loop or human authoring touches one without the
other, the stores drift. `facts reconcile` cross-checks the two and
reports inconsistencies.

> **Prerequisites:**
>
> - `@derive-ltd/uht-substrate` CLI configured.
> - An AIRGen project linked by namespace (substrate `SE:<slug>`
>   matches AIRGen's project slug).
> - `AIRGEN_API_KEY` (or `UHT_AIRGEN_TOKEN` if your deployment uses a
>   distinct credential) available to the substrate so it can read
>   AIRGen.

## What "drift" looks like

The kinds of inconsistency reconciliation surfaces:

- **Stale facts.** A substrate fact references a requirement
  (or entity) that no longer exists in AIRGen.
- **Missing trace links.** Substrate facts imply a relationship (e.g.
  hazard X is mitigated by requirement R-001) but AIRGen has no
  matching trace link.
- **Predicate / link-type mismatch.** Substrate uses one relationship
  type while the corresponding AIRGen trace link uses another.
- **Orphan trace links.** AIRGen has a trace link whose endpoints are
  not represented in the substrate.

None of these are necessarily wrong — they may reflect legitimate
asymmetries — but they are signals worth reviewing.

## 1. Run the reconciliation

```sh
uht-substrate facts reconcile --namespace SE:widget-x
```

The output groups findings by category. A typical run:

```
Reconciliation: SE:widget-x
─────────────────────────────────────────

Stale facts (3)
  · "spark plug" PART_OF "engine" — engine not in AIRGen
  · "wiring harness" CONNECTS "ECU"  — ECU archived in AIRGen
  ...

Missing trace links (5)
  · Substrate: hazard "thermal runaway" mitigated_by "REQ-12"
    AIRGen: REQ-12 has no trace link to hazard "thermal runaway"
  ...

Orphan trace links (1)
  · AIRGen REQ-44 derives REQ-45, neither represented in substrate
```

Pass `--json` for a machine-readable form you can pipe into other
tooling.

## 2. Decide on action per finding

There is no automatic fix. Reconciliation is a *report*; the operator
applies the resolution. A practical playbook:

- **Stale fact, AIRGen entity is gone for good.** Delete or update the
  substrate fact. If many facts reference the deleted entity, consider
  whether the entity should be re-added in AIRGen instead.
- **Missing trace link, substrate is right.** Add the trace link in
  AIRGen (`airgen trace create`) or accept the candidate suggestion
  if Derive has surfaced one.
- **Predicate mismatch.** Decide which name is canonical and update
  the other side. Update Reify if it relies on the predicate too.
- **Orphan trace link.** Either back-fill the substrate facts (often
  via `airgen lint`-driven entity extraction), or accept the
  asymmetry — not all AIRGen traces have a meaningful substrate
  representation.

## 3. Suggested cadence

Reconciliation is cheap; run it often:

- **Per autonomous loop session.** Add it to the post-session hook so
  drift doesn't accumulate silently.
- **Before a baseline.** Catch inconsistencies before they get frozen
  into a release-gate snapshot.
- **Before a delivery.** A clean reconciliation report is a useful
  artefact in regulatory / customer reviews.

## 4. Interpret the count, not just the diff

A growing number of stale facts over time usually means the
autonomous loop is creating substrate facts for entities that get
deleted in AIRGen later — often because the loop's output isn't
being reviewed promptly. Treat the count as a process indicator, not
just a tactical to-do list.

## What's next

- [Storing and querying facts with namespace scoping](./storing-and-querying-facts-with-namespace-scoping.md)
- [Entity lifecycle: reclassify, merge, history, deduplicate](./entity-lifecycle.md)
- [Working with baselines and artefact bundles (Derive)](../../derive/guides/working-with-baselines-and-artefact-bundles.md)
