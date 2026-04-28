# Storing and querying facts with namespace scoping

UHT Substrate's fact store is a typed, namespaced subject–predicate–
object triplestore. This guide covers the data model, the predicates
that emerge in systems-engineering projects, and how to author and
query facts cleanly using the CLI.

> **Prerequisites:** the `@derive-ltd/uht-substrate` CLI installed and
> configured. A project namespace such as `SE:widget-x` (see
> [Classifying entities for a new project](./classifying-entities-for-a-new-project.md)).

## The data model

Every fact is a triple: **subject**, **predicate**, **object**, scoped
to a namespace. For example:

```
subject     : "spark plug"
predicate   : PART_OF
object      : "engine"
namespace   : SE:automotive
```

Predicates are conventionally `UPPER_SNAKE_CASE`. Subject and object
are entity names that exist (or get auto-created) in the namespace.

Two important properties:

- **Predicates are not validated against a schema.** They emerge by
  convention. The substrate will store any predicate name you give it.
- **Object can be a literal.** Some facts have entity-as-object (for
  graph edges); others have a string or numeric literal.

## Predicates that emerge in SE projects

These are the predicates you'll see most often when the autonomous
loop processes a project. They aren't a fixed schema — but using them
consistently lets Reify and other tools reconstruct the structural
view automatically.

| Predicate         | Meaning                                                    |
| ----------------- | ---------------------------------------------------------- |
| `PART_OF`         | Subject is a structural component of object.               |
| `SPEC_TREE`       | Subject sits at this position in the spec tree.            |
| `CONNECTS`        | Subject connects to object (with optional flow type).      |
| `HAS_FUNCTION`    | Subject performs object as a function.                     |
| `FUNCTION_GROUPS_TO` | Function-to-subsystem allocation.                       |
| `HAS_HAZARD`      | Subject is a hazard owned by object (typically a system).  |
| `HAS_MODE`        | Subject is a mode of object.                               |
| `MODE_TRANSITION` | Transition between modes, typically with conditions.       |
| `PRODUCES`        | Subject produces object (a flow or output).                |
| `CONSUMES`        | Subject consumes object (a flow or input).                 |
| `SCENARIO_STEP`   | Step in a use-case scenario.                               |

When in doubt, prefer reusing an existing predicate over inventing a
new one. Convention makes the graph queryable.

## 1. Store a fact

```sh
uht-substrate facts store "spark plug" PART_OF "engine" \
  --namespace SE:automotive
```

The CLI returns the canonical fact ID. If the subject or object don't
exist yet, the substrate creates placeholder entities; you can
classify them later.

For facts that should never duplicate, prefer `upsert`:

```sh
uht-substrate facts upsert "spark plug" PART_OF "engine" \
  --namespace SE:automotive
```

`upsert` is idempotent — running it twice does not create two copies.

## 2. Bulk-store from JSON

For initial loads or batch updates, write a JSON array and pipe it in:

```json
[
  { "subject": "bolt",        "predicate": "PART_OF",  "object_value": "frame" },
  { "subject": "frame",       "predicate": "PART_OF",  "object_value": "chassis" },
  { "subject": "chassis",     "predicate": "PART_OF",  "object_value": "vehicle" }
]
```

```sh
uht-substrate facts store-bulk -f facts.json --namespace SE:automotive

# Or piped:
cat facts.json | uht-substrate facts store-bulk -f - --namespace SE:automotive
```

Bulk store is significantly faster than a `store` per fact for
hundreds or thousands of triples.

## 3. Query with filters

```sh
# All facts whose subject is "spark plug"
uht-substrate facts query --subject "spark plug" --namespace SE:automotive

# All PART_OF edges in the namespace
uht-substrate facts query --predicate PART_OF --namespace SE:automotive

# All facts whose object is "engine"
uht-substrate facts query --object "engine" --namespace SE:automotive

# Compositional category, return everything (paginated)
uht-substrate facts query \
  --namespace SE:automotive \
  --category compositional \
  --limit all
```

The query response includes a total count, which is useful for
pagination.

## 4. Get the whole picture for a namespace

```sh
uht-substrate facts namespace-context SE:automotive
```

Returns every entity and fact under the namespace as a single
document. Useful for:

- Spot-checking coverage after the autonomous loop runs.
- Generating a project-state snapshot for review.
- Feeding into downstream tooling (Reify uses similar queries to
  reconstruct SysML v2).

For per-user (rather than per-namespace) facts, use:

```sh
uht-substrate facts user-context
```

## 5. Update or delete a fact

Both operations need the canonical fact ID, which `query` returns:

```sh
uht-substrate facts update <fact-id> --object "new value"
uht-substrate facts delete <fact-id>
```

Deleting a fact does not delete the entities it references. Use
`entities delete` for that, but be aware that deleting an entity
unbinds (rather than cascade-deletes) any facts that reference it.

## 6. Common patterns

- **Bulk-load the structural skeleton, then layer.** Start by bulk
  storing all `PART_OF` and `SPEC_TREE` facts to define the
  hierarchy, then layer in `HAS_FUNCTION`, `CONNECTS`, and behavioural
  predicates.
- **Use `upsert` for autonomous-loop output.** Idempotency means you
  can re-run sessions safely without accumulating duplicates.
- **Keep predicates stable.** Don't invent variations like
  `IS_PART_OF` and `PART_OF` — Reify and Derive expect the
  conventional names.

## What's next

- [Reconciling substrate facts against AIRGen trace links](./reconciling-substrate-facts-against-airgen-trace-links.md)
- [Entity lifecycle: reclassify, merge, history, deduplicate](./entity-lifecycle.md)
