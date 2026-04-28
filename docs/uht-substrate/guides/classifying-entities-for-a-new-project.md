# Classifying entities for a new project

This guide walks through classifying the entities of a
systems-engineering project into the Universal Hex Taxonomy. By the
end you'll have a project namespace, a handful of classified
entities, and an understanding of how to read the trait breakdown.

> **Prerequisites:** the `@derive-ltd/uht-substrate` CLI installed and
> configured with a Bearer token. See the
> [getting-started guide](../getting-started.md) for setup.

## The classification model in one paragraph

Every entity is evaluated against **32 binary traits** organised into
four semantic layers — Physical (bits 1–8), Functional (bits 9–16),
Abstract (bits 17–24), and Social (bits 25–32). The result is an
8-character hex code. Two entities with similar codes share similar
properties, and Jaccard similarity over the bit vectors gives a
meaningful overlap score.

For systems-engineering projects, the convention is to scope all
entities and facts under a project-specific namespace, typically
`SE:<project-slug>`.

## 1. Pick a namespace

Namespaces keep your project's entities and facts separate from the
global corpus and from other projects. The convention is two-tier:
`SE:<slug>` (where `SE` is the systems-engineering top-level
namespace and `<slug>` identifies your project).

```sh
uht-substrate namespaces create SE:widget-x "Widget X — Power Subsystem"
```

If `SE:` doesn't exist yet, the substrate creates it implicitly when
you create the child.

List existing namespaces:

```sh
uht-substrate namespaces list
uht-substrate namespaces list --parent SE --descendants
```

## 2. Classify your first entity

Pick a noun from your project — a component, an actor, a flow, a
mode. Classify it in your namespace:

```sh
uht-substrate classify "thermal radiator" \
  --namespace SE:widget-x \
  --format pretty
```

You'll get back something like:

```
thermal radiator
hex   : C8841201
namespace : SE:widget-x

Physical    : Physical Object · Synthetic · Powered · Mechanical
Functional  : Intentionally Designed · State-Transforming · Outputs Effect
Abstract    : (none)
Social      : Economically Significant
```

The hex code is the canonical identifier; the trait list is the
human-readable breakdown.

## 3. Use `--context` for ambiguous entities

Bare nouns are often ambiguous. "Bank" might be a financial
institution, a riverbank, or a tilt manoeuvre. Provide a one-line
context to disambiguate:

```sh
uht-substrate classify "bank" \
  --context "the act of tilting an aircraft about its longitudinal axis" \
  --namespace SE:widget-x \
  --format pretty
```

Without `--context`, the classifier picks the most common sense; with
it, you steer the classification.

If you want the classifier's full thinking on word senses before
committing:

```sh
uht-substrate disambiguate bank
```

## 4. Reclassify with `--force` when you change context

Classifications are cached. If you need to re-run a classification
because the context has changed, use `--force`:

```sh
uht-substrate classify "thermal radiator" \
  --context "subsea-rated, hydraulic-loop, 1 m² fin area" \
  --namespace SE:widget-x \
  --force
```

The entity's UUID and any facts attached to it are preserved — only
the trait vector and hex code update. Use `entities history` to see
the lineage:

```sh
uht-substrate entities history "thermal radiator"
```

## 5. Bulk classify a list

In practice you rarely classify one entity. The CLI accepts piped
input for batch operations. A typical workflow is:

1. Extract a list of nouns from your requirements (using `airgen lint`
   or your own tooling).
2. Pipe each through `classify`, scoped to your namespace.

```sh
# Suppose entities.txt is one entity per line
while read entity; do
  uht-substrate classify "$entity" --namespace SE:widget-x
done < entities.txt
```

For larger batches, prefer the REST API or MCP server — both support
parallel calls and avoid the per-invocation CLI startup cost.

## 6. Verify the namespace contents

Once you've classified a useful set, confirm what's there:

```sh
uht-substrate entities list --namespace SE:widget-x --limit all
uht-substrate facts namespace-context SE:widget-x
```

`namespace-context` returns every entity and fact in the namespace —
useful to spot-check coverage before moving on to fact authoring.

## 7. Common patterns

- **Classify-as-you-go.** Classify each entity the moment you write a
  requirement that mentions it. This keeps classifications grounded in
  your specific context rather than the global corpus.
- **Classify-from-list.** Maintain a project glossary and classify the
  glossary in batch. Lower friction at requirement-authoring time.
- **Periodic reclassification.** As the project matures and constraints
  change, reclassify key entities with richer context. Use
  `entities history` to audit drift.

## What's next

- [Storing and querying facts with namespace scoping](./storing-and-querying-facts-with-namespace-scoping.md)
- [Entity lifecycle: reclassify, merge, history, deduplicate](./entity-lifecycle.md)
- [Reconciling substrate facts against AIRGen trace links](./reconciling-substrate-facts-against-airgen-trace-links.md)
