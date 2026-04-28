# Working with baselines and artefact bundles

A baseline freezes the project at a point in time — the requirements,
trace links, substrate facts, verification activities, and SysML
source as they exist on that date. An artefact bundle is the
exportable packaging of that baseline: Word, PDF, Excel, or a zipped
package suitable for delivery or audit.

This guide walks through creating a clean baseline, generating
artefacts, and the conventions that keep baselines useful as a
historical record.

> **Prerequisites:** a Derive project with a passing
> [quality gate](./the-quality-view.md) and at least one populated
> document. Permission to create baselines is typically restricted
> to senior engineers.

## What's in a baseline

Baselines are point-in-time snapshots that capture:

- **Every requirement** at its current state and version.
- **Every trace link** active at the baseline timestamp.
- **The verification matrix** as it stands.
- **The SysML v2 source** (canonical, not workspace).
- **Substrate facts** under the project's namespace.
- **Diagrams** with their per-baseline viewports.

What baselines do *not* capture:

- The autonomous loop's session history (that lives in the journal).
- User permissions and project metadata.
- Other projects in the same tenant.

Baselines are immutable — once created, they cannot be edited. To
"correct" a baseline, create a new baseline with the corrected state
and supersede the old one in the release notes.

## 1. Get to a clean state

Before creating a baseline:

- **Pass the quality gate.** Don't baseline a red gate.
- **Resolve orphan trace links.** Either link them or mark them
  intentional.
- **Reconcile substrate vs AIRGen.** Run `uht-substrate facts
  reconcile` and act on findings.
- **Pause the autonomous loop.** Avoid mid-baseline session writes.
- **Commit the SysML workspace.** Workspaces aren't included in
  baselines; canonical source is.

A clean state means anyone reading the baseline later sees a
coherent project, not a snapshot mid-restructure.

## 2. Create the baseline

### From Derive

`/p/<slug>/dashboard` → **Baselines → New**. Pick a name (use a
release tag like `v1.0` or `2026-04-28-pre-review`) and a
description. Derive computes the baseline (a few seconds) and adds
it to the baseline list.

### From the CLI

```sh
airgen bl create acme widget-x --name "v1.0"
```

Returns the baseline ID. The CLI is convenient for scripting
release-cut workflows in CI.

## 3. Verify the baseline

Right after creation, **diff against the previous baseline**:

```sh
airgen diff acme widget-x --from v0.9 --to v1.0
```

You should see exactly the changes you expect — no surprise deletes,
no orphaned requirements, no stale facts. If something looks wrong,
investigate before publishing the baseline.

## 4. Generate artefacts

Derive's **Artifacts** tab on the project page exports baselines as:

- **Word documents.** A formatted requirements document with section
  hierarchy, suitable for customer / regulator delivery.
- **PDF.** Same content, frozen for archival. PDFs are the canonical
  delivery format for most regulated industries.
- **Excel.** Tabular requirements export — useful for
  spreadsheet-driven reviewers.
- **Zipped bundles.** A single archive containing the SysML source,
  requirements, traces, baseline metadata, and any attached
  evidence. The standard delivery format for an audit pack.

Pick the formats your audience needs. For most regulated deliveries:

- PDF (the formal document)
- Zipped bundle (the data pack)
- Optional Word (if the customer wants editable text)

### Bundles via the CLI

```sh
# Excel export
airgen export requirements acme widget-x --format excel -o widget-x.xlsx

# Markdown (good for git-tracked changelogs)
airgen export requirements acme widget-x > widget-x.md

# Or use the Derive bundle script
/path/to/derive/scripts/generate-bundle-zip.py \
    --project widget-x \
    --baseline v1.0 \
    -o widget-x-v1.0.zip
```

For ISO-style PDFs:

```sh
/path/to/derive/scripts/generate-iso-pdf.py \
    --project widget-x \
    --baseline v1.0 \
    -o widget-x-v1.0.pdf
```

## 5. Compare baselines

The most useful operation post-creation is comparison.

### From Derive

`/p/<slug>/baselines` → pick two → **Compare**. Derive shows a diff
view with added / modified / removed requirements, trace-link deltas,
and SysML source diff.

### From the CLI

```sh
# Pretty terminal diff
airgen diff acme widget-x --from v0.9 --to v1.0

# JSON for automation
airgen diff acme widget-x --from v0.9 --to v1.0 --json

# Markdown release notes
airgen diff acme widget-x --from v0.9 --to v1.0 \
  --format markdown -o RELEASE_NOTES.md
```

The markdown form is particularly useful — drop it into your release
PR description and the reviewer sees exactly what changed.

## 6. Conventions for baseline naming

A baseline name lives forever. A few conventions that age well:

- **Semver-style for delivery baselines.** `v1.0`, `v1.1`, `v2.0`.
- **ISO-date for snapshots.** `2026-04-28-pre-review`,
  `2026-Q2-mid`.
- **Tag for milestones.** `pre-PDR`, `pre-CDR`, `post-FAA-audit`.

Avoid:

- **Internal jargon.** `bl-bob-fri-pm` won't mean anything in three
  months.
- **Numbers without context.** `42` is not informative.
- **Names that imply state changes.** `final` is rarely final.

## 7. Restore a previous baseline

Baselines are immutable, so "restore" means "create a new state that
matches the old baseline". This is how you back out a bad change set.

### From Derive

`/p/<slug>/baselines` → pick a baseline → **Restore**. Derive
creates a new working state matching the baseline; current
requirements not in the baseline are archived (not deleted).

### From the CLI

```sh
airgen bl restore acme widget-x --baseline v0.9
```

Both forms are reversible — the previous state is itself baselineable
beforehand if you want a way back.

## A typical release-cut workflow

```sh
RELEASE=v1.4
LAST=$(airgen bl list acme widget-x --json | jq -r '.[0].name')

# 1. Pause the loop, polish quality
# (Derive UI, or the harness directive panel)

# 2. Create the baseline
airgen bl create acme widget-x --name "$RELEASE"

# 3. Diff for the release notes
airgen diff acme widget-x \
  --from "$LAST" --to "$RELEASE" \
  --format markdown -o RELEASE_NOTES.md

# 4. Bundle for delivery
generate-iso-pdf.py --project widget-x --baseline "$RELEASE" \
    -o widget-x-$RELEASE.pdf

generate-bundle-zip.py --project widget-x --baseline "$RELEASE" \
    -o widget-x-$RELEASE.zip

# 5. Tag the corresponding code repo
git tag "$RELEASE"
```

Run this as a release script and your delivery pack is reproducible
on demand.

## What's next

- [Reading the per-project dashboard](./reading-the-per-project-dashboard.md)
- [The quality view](./the-quality-view.md)
- [Driving the autonomous loop](./driving-the-autonomous-loop.md)
- [Configuring the two-track backup model (AIRGen)](../../airgen/guides/configuring-the-two-track-backup-model.md)
