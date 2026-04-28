# Building a traceability matrix

A traceability matrix maps every system requirement to the
stakeholder needs it satisfies, the lower-level requirements it
derives, and the verification activities that will demonstrate it.
AIRGen's graph database makes the matrix navigable rather than
spreadsheet-like — you can ask questions like "which stakeholder
needs are not covered?" and get a real answer.

This guide walks through building a clean trace structure for a new
project.

> **Prerequisites:** an AIRGen project with at least a couple of
> documents. See
> [Authoring your first requirement](./authoring-your-first-requirement.md)
> if you're starting from blank.

## The link types

AIRGen supports six typed trace links:

| Link type    | Direction                            | Meaning                                                    |
| ------------ | ------------------------------------ | ---------------------------------------------------------- |
| `derives`    | `<derived> derives from <parent>`    | Source is a refinement of target.                          |
| `satisfies`  | `<lower> satisfies <higher>`         | Source meets the intent of target.                         |
| `verifies`   | `<verification> verifies <req>`      | Source produces evidence target is met.                    |
| `implements` | `<implementation> implements <req>`  | Source fulfils target in the implementation.               |
| `refines`    | `<more-specific> refines <general>`  | Source narrows the meaning of target.                      |
| `conflicts`  | `<a> conflicts with <b>`             | A and B cannot both be satisfied — flag for resolution.    |

The directions matter. AIRGen uses them to compute coverage and to
walk the graph in either direction.

## A working layout

A typical regulated-systems project ends up with roughly these layers:

```
stakeholder-requirements (the "what we need" needs)
        │  derives
        ▼
system-requirements (the "what the system must do")
        │  derives
        ▼
sub-system-requirements (per-subsystem refinements)
        │  derives
        ▼
component-requirements (per-component constraints)

Crossing layers:
- interface-requirements (between systems / subsystems)
  satisfies / refines pairs of layered requirements.
- safety-requirements / hazard-mitigation
  satisfies system or sub-system requirements.

Verification:
- verification-activities verify any requirement layer.
```

You don't need every layer. Two-layer projects (stakeholder →
system) are common; six-layer projects exist for safety-critical
systems. Pick the depth your domain warrants.

## 1. Author the parent layer first

Don't try to write trace links bottom-up. Author the stakeholder
requirements (or the highest layer you have) first, with no parent
links. Get them to a reasonable QA score before deriving children.

## 2. Derive children with traces from day one

When you write a system requirement, link it to its stakeholder
parent immediately. Two ways:

### Web UI

Open the system requirement, click **Trace → New**, set:

- Source: the system requirement
- Target: the stakeholder requirement
- Link type: `derives`

### CLI

```sh
airgen trace create <tenant> <project> \
  --source SYS-001 \
  --target STK-007 \
  --type derives
```

Use the CLI for bulk operations (e.g. mass-deriving twenty
sub-system requirements from one system requirement at the start of
a project).

## 3. Add cross-cutting traces

Some traces don't fit the strict layer model — interfaces, safety
mitigations, conflicts. Add them as you go:

- **Interface links.** When subsystem A's interface requirement
  matches subsystem B's, link with `satisfies` on both sides.
- **Safety mitigations.** A `safety-requirement` `satisfies` the
  `system-requirement` it mitigates a hazard for.
- **Conflicts.** When you find two requirements that can't both hold,
  use `conflicts`. AIRGen flags conflicts in the dashboard until they
  are resolved (one is dropped or both are refined).

## 4. Link verification activities

In **Verification → Activities**, every activity should `verify` at
least one requirement:

```sh
airgen verify act create <tenant> <project> SYS-001 \
  --method Test \
  --title "Emergency stop response time test"
```

The matrix view (`airgen verify matrix <tenant> <project>`) shows
which requirements have at least one verification activity, which
have evidence attached, and which are uncovered.

## 5. Linksets — group related links

A linkset is a named collection of trace links. Useful for:

- Per-document linksets (e.g. "interface-control-document").
- Per-baseline frozen linksets.
- Per-customer or per-deliverable groupings.

```sh
airgen trace linksets list <tenant> <project>
```

Linksets don't change the underlying graph; they're a tag for views
and exports.

## 6. Find orphans before reviews

The single most useful trace report is the orphan list:

```sh
airgen report orphans <tenant> <project>
```

It returns every requirement with no incoming or no outgoing trace
links, by document. Common causes:

- Requirement copied from another project but not re-traced.
- Stakeholder need that was never broken down.
- System requirement that nobody verified.

Walk the list before each review and resolve — either link, mark as
intentionally untraced (with justification), or delete.

## 7. Build the report

```sh
# A summary report
airgen report stats <tenant> <project>

# Compliance + implementation status
airgen report compliance <tenant> <project>

# Quality (QA score) summary
airgen report quality <tenant> <project>
```

For a printable matrix, export to JSON or markdown:

```sh
airgen report compliance <tenant> <project> --json | jq ...
airgen export requirements <tenant> <project>     # Markdown
```

## Common patterns

- **Trace as you author.** It's an order of magnitude faster than
  trying to back-fill traces a week later.
- **Use AI suggestions sparingly.** AIRGen's graph-based link
  recommendations are useful for suggesting candidates, but they
  shouldn't be accepted without thought — every accepted link has
  semantic weight.
- **Run orphan reports weekly.** They are a good leading indicator
  of project drift.
- **Freeze linksets at baselines.** A baselined linkset is what you
  hand to a regulator or customer as the as-shipped trace map.

## What's next

- [Authoring your first requirement](./authoring-your-first-requirement.md)
- [Understanding the QA score and EARS patterns](./understanding-the-qa-score-and-ears-patterns.md)
- [Using the AIRGen CLI](./using-the-airgen-cli.md)
