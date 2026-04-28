# Specification Bundle — EEG Accessibility Buggy (2026-04-28)

A point-in-time snapshot of the artefacts produced by the
[`Exports` page](https://derive.airgen.studio/p/se-eeg-accessibility-buggy/downloads)
for Derive's public demo project, taken on **2026-04-28**.

> **Classification:** UNCLASSIFIED — SYNTHETIC DATA. The demo
> project is a fictional system built for evaluation purposes; the
> requirements, hazards, and design content do not describe a real
> product.

## Why these files specifically

The platform's "Complete Specification Bundle" includes nine files
totalling 5.3 MB. We've trimmed it to the artefacts that render
natively on github.com so a casual reader can scroll through the
example without leaving the docs site.

The trim is **substitution, not omission**: each ISO 15289
document is included as its `.md` form (one of the four formats
the platform offers per document) instead of the `.pdf` form.

| File                                  | Form    | From the bundle? |
| ------------------------------------- | ------- | ---------------- |
| `MANIFEST.txt`                        | text    | yes (original)   |
| `00-Cover-Letter.pdf`                 | PDF     | yes (kept — sets the bundle's visual style) |
| `01-ConOps.md`                        | Markdown| swapped from `01-ConOps.pdf` |
| `02-Requirements-Specification.md`    | Markdown| swapped from `02-Requirements-Specification.pdf` |
| `03-Design-Description.md`            | Markdown| swapped from `03-Design-Description.pdf` |
| `04-Verification-Plan.md`             | Markdown| swapped from `04-Verification-Plan.pdf` |
| `05-Requirements-Export.xlsx`         | Excel   | yes (kept — Excel has no markdown form) |
| `07-Engineering-Log.md`               | Markdown| swapped from `07-Engineering-Log.pdf` |

## Not included

- `06-Requirements-Export.reqif` (1 MB of XML — meaningful only
  to ReqIF-aware tooling).
- The `.pdf` forms of the four ISO 15289 documents and the
  Engineering Log.

For any of the above, fetch the live bundle from
[`derive.airgen.studio/p/se-eeg-accessibility-buggy/downloads`](https://derive.airgen.studio/p/se-eeg-accessibility-buggy/downloads).

## Refresh policy

This snapshot will not be auto-updated. The demo project at
`/demo` evolves over time — re-fetch from the live Exports page
when you need current artefacts. Future snapshots in this
directory should land in dated sibling folders rather than
overwriting these files, so the history of demo content stays
visible.
