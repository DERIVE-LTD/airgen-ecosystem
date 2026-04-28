# Using the `@derive-ltd/airgen-cli` CLI

The `airgen` CLI is the terminal interface to AIRGen. It mirrors
nearly the full surface of the web UI — requirements, documents,
diagrams, traceability, baselines, quality, verification,
implementation tracking, import / export — and bundles an MCP server
in the same package for Claude Desktop wiring.

This guide is an end-to-end walkthrough of the commands you will
actually use, with the conventions that make scripting against
AIRGen pleasant.

> **Prerequisites:** Node.js ≥ 18. An AIRGen account (or a
> self-hosted instance) and either an API key (`ak_...`) or
> email + password.

## Install

```sh
npm install -g @derive-ltd/airgen-cli
```

This adds two binaries to your PATH:

| Bin          | Purpose                                  |
| ------------ | ---------------------------------------- |
| `airgen`     | Interactive CLI for everyday use.        |
| `airgen-mcp` | MCP server for Claude Desktop / Code.    |

The unscoped `airgen-cli` package on npm is the legacy publish line —
use the `@derive-ltd/airgen-cli` scope for new installs.

## Configure

Pick whichever style fits your workflow.

### Environment variables

```sh
export AIRGEN_API_URL=https://api.airgen.studio/api
export AIRGEN_API_KEY=ak_...                # preferred
# or:
export AIRGEN_EMAIL=you@example.com
export AIRGEN_PASSWORD=your-password
```

### `~/.airgenrc`

```json
{
  "apiUrl": "https://api.airgen.studio/api",
  "email": "you@example.com",
  "password": "your-password"
}
```

For semantic linting (`airgen lint`), also set a UHT Substrate token:

```sh
export UHT_API_KEY=your-token    # or UHT_TOKEN
```

## The command shape

Most commands take `<tenant>` and `<project>` as positionals, then
flags. So you'll repeat the tenant and project a lot — that's
intentional, because the CLI is stateless: there is no "current
project" to forget about. For convenience, set them in env vars:

```sh
export AIRGEN_TENANT=acme
export AIRGEN_PROJECT=widget-x
```

(If your fork supports tenant / project defaults via env or rc file,
check `airgen --help` for the exact names; otherwise pass explicitly.)

Append `--json` to any command for machine-readable output:

```sh
airgen reqs list acme widget-x --json | jq '.[].ref'
```

## Tenants and projects

```sh
airgen tenants list
airgen projects list acme
airgen projects create acme --name "Widget X"
airgen projects delete acme widget-x
```

## Requirements

The everyday commands:

```sh
# List with paging
airgen reqs list acme widget-x
airgen reqs list acme widget-x --page 2 --limit 50

# Read one
airgen reqs get acme widget-x SYS-001

# Create
airgen reqs create acme widget-x \
  --text "When the operator presses STOP, the system shall halt within 100 ms."

# Update
airgen reqs update acme widget-x SYS-001 \
  --text "When the operator..." \
  --tags safety,critical

# Delete (soft)
airgen reqs delete acme widget-x SYS-001

# Version history
airgen reqs history acme widget-x SYS-001

# Search
airgen reqs search acme widget-x --query "thermal" --mode semantic

# Filter
airgen reqs filter acme widget-x \
  --pattern functional --tag safety --score-max 60
```

Searches return ranked matches. Filtering is multi-field — combine
`--pattern`, `--tag`, `--document`, `--score-min` / `--score-max`,
and free-text query.

## Architecture diagrams

```sh
airgen diag list acme widget-x
airgen diag get acme widget-x diagram-123
airgen diag render acme widget-x diagram-123                  # terminal
airgen diag render acme widget-x diagram-123 --format mermaid
airgen diag render acme widget-x diagram-123 --format mermaid --wrap -o diagram.md
```

Mermaid output is convenient for embedding in markdown docs or
GitHub READMEs. Use `--wrap` to wrap the result in fenced code
blocks.

Blocks and connectors:

```sh
airgen diag blocks library acme widget-x
airgen diag blocks create acme widget-x \
  --diagram diagram-123 \
  --name "Power Subsystem" --kind subsystem
airgen diag conn create acme widget-x \
  --diagram diagram-123 \
  --source block-1 --target block-2 \
  --kind flow --label "data"
```

## Traceability

```sh
airgen trace list acme widget-x

airgen trace create acme widget-x \
  --source SYS-001 --target STK-007 --type derives

airgen trace delete acme widget-x trace-id

airgen trace linksets list acme widget-x
```

For more on trace topology, see
[Building a traceability matrix](./building-a-traceability-matrix.md).

## Baselines and diff

```sh
airgen bl list acme widget-x
airgen bl create acme widget-x --name "v1.0"
airgen bl compare acme widget-x --from bl-1 --to bl-2

# Rich diff
airgen diff acme widget-x --from bl-1 --to bl-2
airgen diff acme widget-x --from bl-1 --to bl-2 --json
airgen diff acme widget-x --from bl-1 --to bl-2 \
  --format markdown -o diff.md
```

`diff` shows added, modified, and removed requirements with full
text plus summaries of changes to documents, trace links, diagrams,
blocks, and connectors. Use `--format markdown -o file.md` to write
release notes directly.

## Quality and AI

```sh
# One-off QA on a sentence
airgen qa analyze "When the operator presses STOP, ..."

# Project-wide background QA
airgen qa score start acme widget-x

# AI drafting
airgen qa draft "the operator needs an emergency stop"

# Candidates workflow
airgen ai generate acme widget-x --prompt "..."
airgen ai candidates acme widget-x
airgen ai accept candidate-id
airgen ai reject candidate-id
```

## Reports

```sh
airgen report stats acme widget-x         # overview
airgen report quality acme widget-x       # QA summary
airgen report compliance acme widget-x    # compliance + impl
airgen report orphans acme widget-x       # untraced requirements
```

All report commands auto-paginate the full requirement set up to
~5000.

## Implementation tracking

```sh
airgen impl status acme widget-x SYS-001 \
  --status implemented --notes "shipped in v2"

airgen impl summary acme widget-x
airgen impl list acme widget-x --status blocked
airgen impl bulk-update acme widget-x --file updates.json

# Artifact links (code files, design notes)
airgen impl link acme widget-x SYS-001 --type file --path src/engine.ts
airgen impl unlink acme widget-x SYS-001 --artifact artifact-id
```

Statuses: `not_started`, `in_progress`, `implemented`, `verified`,
`blocked`.

## Verification

The TADI taxonomy — Test, Analysis, Demonstration, Inspection.

```sh
# Engine
airgen verify run acme widget-x         # full coverage report
airgen verify matrix acme widget-x      # cross-reference

# Activities
airgen verify act list acme widget-x
airgen verify act create acme widget-x SYS-001 \
  --method Test --title "Stop response test"
airgen verify act update activity-id --status passed

# Evidence
airgen verify ev list acme widget-x
airgen verify ev add acme widget-x activity-id \
  --type test_result --title "Bench test 2026-04-28" \
  --verdict pass --recorded-by "S. Holland"

# Verification documents
airgen verify docs create acme widget-x \
  --name "Test Plan" --kind test_plan
airgen verify docs status vdoc-id --status approved
airgen verify docs revise vdoc-id \
  --rev 1.0 --change "Final review" --by "S. Holland"
```

## Import / export

```sh
airgen import requirements acme widget-x --file reqs.csv
airgen export requirements acme widget-x         # markdown
airgen export requirements acme widget-x --json  # JSON
```

## Semantic lint

`airgen lint` classifies the domain concepts in your requirements
through UHT Substrate and flags ontological mismatches, ambiguity,
and coverage gaps.

```sh
airgen lint acme widget-x
airgen lint acme widget-x --concepts 20
airgen lint acme widget-x --format markdown -o lint-report.md
```

It needs a UHT token (`UHT_TOKEN` or `UHT_API_KEY`).

## Aliases

| Full          | Alias    |
| ------------- | -------- |
| `requirements`| `reqs`   |
| `diagrams`    | `diag`   |
| `documents`   | `docs`   |
| `connectors`  | `conn`   |
| `baselines`   | `bl`     |
| `traces`      | `trace`  |
| `quality`     | `qa`     |
| `reports`     | `report` |
| `projects`    | `proj`   |
| `sections`    | `sec`    |

## Scripting patterns

The CLI is built for piping. A few real-world patterns:

```sh
# Find all orphans, write them to a file
airgen report orphans acme widget-x --json \
  | jq -r '.items[] | "\(.ref)\t\(.text)"' \
  > orphans.tsv

# Rebaseline before a release tag
RELEASE=v1.4
airgen bl create acme widget-x --name "$RELEASE"
git tag "$RELEASE"

# Generate a diff markdown for the PR
airgen diff acme widget-x \
  --from "$LAST_BL" --to "$RELEASE" \
  --format markdown -o RELEASE_NOTES.md
```

## What's next

- [Connecting AIRGen to Claude via the MCP server](./connecting-airgen-to-claude-via-the-mcp-server.md)
- [Building a traceability matrix](./building-a-traceability-matrix.md)
- [Understanding the QA score and EARS patterns](./understanding-the-qa-score-and-ears-patterns.md)
