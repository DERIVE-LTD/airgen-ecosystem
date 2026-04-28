# Entity lifecycle: reclassify, merge, history, deduplicate

Entities accumulate, drift, and duplicate over the life of a project.
The substrate exposes a small set of lifecycle operations that keep
the namespace tidy without breaking the facts that reference each
entity.

> **Prerequisites:** the `@derive-ltd/uht-substrate` CLI configured,
> and a project namespace such as `SE:widget-x`.

## The lifecycle problem

An autonomous loop running for weeks across many sessions will
inevitably:

- **Re-classify entities** as their context grows richer (add
  constraints, add interfaces) — the original hex code may become
  inaccurate.
- **Create duplicates** when prompts use different wording for the
  same concept ("ECU" vs "engine control unit").
- **Leak entities into the global namespace** when a prompt forgets
  to scope to `SE:<slug>`.

Each of the operations below addresses one of these.

## `entities update` — fix metadata

```sh
uht-substrate entities update "spark plug" \
  --description "Ignition component, single-use, hex 14 mm" \
  --display-name "Spark Plug"
```

Updates description and display name without touching the trait
vector or any facts. Cheap, reversible — use freely.

## `entities reclassify` — re-run classification, preserve UUID

When you want a new trait vector for an existing entity (because the
context has matured), use `reclassify` rather than deleting and
re-creating:

```sh
uht-substrate entities reclassify "thermal radiator" \
  --context "subsea-rated, hydraulic-loop, 1 m² fin area" \
  --namespace SE:widget-x
```

What gets preserved:

- The UUID (so all facts referencing the entity remain valid)
- Existing facts
- Display name

What changes:

- The hex code
- The 32 trait bits
- The classification history (a new entry is appended)

To audit the change:

```sh
uht-substrate entities history "thermal radiator"
```

Returns the lineage of hex codes with timestamps. Useful when a
downstream consumer (Reify, Derive's spec-tree) starts behaving
differently and you want to know whether classification drift caused
it.

## `entities merge` — consolidate duplicates

Two entities for the same concept is a common ask. `merge` transfers
all facts and relationships from the source entity into the target,
then deletes the source:

```sh
uht-substrate entities merge "ECU" "engine control unit"
```

The first argument is the entity that gets removed; the second is the
canonical entity that absorbs everything.

**Order of arguments matters.** Merging in the wrong direction can
move all your facts onto a less-canonical name. If unsure, run
`entities get --name <name>` for both and compare which has the
better display name and description.

After a merge:

- The source name no longer resolves.
- Every fact that referenced the source now references the target.
- The source's UUID is gone — anything tracking facts by entity name
  continues to work; anything tracking by source UUID will not.

## `entities history` — audit classification changes

```sh
uht-substrate entities history "thermal radiator"
```

Returns the chain of classifications:

```
thermal radiator
─────────────────
2026-04-10 10:14  C8841201   (initial)
2026-04-22 17:03  C8841301   reclassified — context "subsea-rated"
2026-04-28 09:11  C8841311   reclassified — context "1 m² fin area"
```

Read this when:

- A diagram in Reify suddenly looks different — check whether the
  hex changed and a downstream view re-binned the entity.
- An audit needs to show how a classification evolved.
- You're considering reverting a classification — `reclassify` again
  with the older context.

## `entities deduplicate` — reclaim leaked entities

Entities sometimes end up in the global (root) namespace by mistake —
e.g. a prompt forgot to specify `--namespace`. `deduplicate` finds
entities whose name matches a global entity, scoped to a target
namespace:

```sh
uht-substrate entities deduplicate --namespace SE:widget-x
```

Output is a list of name collisions: an entity in `SE:widget-x` and
an entity in the global namespace with the same name. The CLI prompts
to confirm before merging the global one into the namespaced one (or
you can pass a flag to auto-confirm in scripts).

Run this:

- After ingesting a large batch.
- After a long autonomous-loop run.
- Before a baseline — namespace hygiene is a good preface to a
  release.

## `entities delete` — last resort

```sh
uht-substrate entities delete "spark plug"
```

Removes the entity from the namespace. Any facts referencing it are
**unbound** (the reference becomes dangling) rather than cascade-
deleted — this is intentional, so you don't accidentally lose
information. Resolve the dangling facts separately.

Prefer `merge` over `delete` when there's a canonical alternative.

## Suggested cadence

| Operation       | When to run                                                |
| --------------- | ---------------------------------------------------------- |
| `update`        | As metadata improves; cheap.                               |
| `reclassify`    | When a richer context is available; before a baseline.     |
| `merge`         | When duplicates are spotted (often after `airgen lint`).   |
| `history`       | On demand, when investigating drift.                       |
| `deduplicate`   | After bulk ingests; before baselines.                      |
| `delete`        | Rare. Prefer merge.                                        |

## What's next

- [Classifying entities for a new project](./classifying-entities-for-a-new-project.md)
- [Storing and querying facts with namespace scoping](./storing-and-querying-facts-with-namespace-scoping.md)
- [Reconciling substrate facts against AIRGen trace links](./reconciling-substrate-facts-against-airgen-trace-links.md)
