# `@derive-ltd` packages on npm

A directory of the developer-facing packages Derive Ltd publishes
under the `@derive-ltd` scope. Each package pairs with one of the
ecosystem components and ships either a CLI, an MCP server, or both.

For deep usage and tutorials, follow the links into the product
pages. This page is an install-and-go reference.

## At a glance

| Package                                                                                       | Latest | Pairs with                            | Ships a CLI | Ships an MCP server         |
| --------------------------------------------------------------------------------------------- | ------ | ------------------------------------- | ----------- | --------------------------- |
| [`@derive-ltd/airgen-cli`](https://www.npmjs.com/package/@derive-ltd/airgen-cli)             | 1.1.1  | [AIRGen](../airgen/)                  | ✓ `airgen`  | ✓ `airgen-mcp` (since 1.1.0) |
| [`@derive-ltd/reify`](https://www.npmjs.com/package/@derive-ltd/reify)                       | 1.1.1  | [Reify](../reify/)                    | ✓ `reify`   | ✓ `reify-mcp`               |
| [`@derive-ltd/uht-substrate`](https://www.npmjs.com/package/@derive-ltd/uht-substrate)       | 1.0.3  | [UHT Substrate](../uht-substrate/)    | ✓ `uht-substrate` | — _(MCP is hosted; see below)_ |

The legacy unscoped names (`airgen-cli`, `uht-substrate`, `reify-mcp`)
remain on npm but are no longer maintained — use the `@derive-ltd`
scope for new work.

## Install

```sh
# Globally
npm install -g @derive-ltd/airgen-cli @derive-ltd/reify @derive-ltd/uht-substrate

# Or run any of them ad-hoc with npx
npx -y @derive-ltd/airgen-cli airgen tenants list
npx -y @derive-ltd/reify reify list-projects
npx -y @derive-ltd/uht-substrate classify hammer
```

All three target Node.js ≥ 18, ship as ESM, and are MIT-licensed.

## Configuration matrix

| Package                       | Env vars                                                               | Config file       | Notes                                                                 |
| ----------------------------- | ---------------------------------------------------------------------- | ----------------- | --------------------------------------------------------------------- |
| `@derive-ltd/airgen-cli`      | `AIRGEN_API_URL`, `AIRGEN_API_KEY` _(or `AIRGEN_EMAIL` + `AIRGEN_PASSWORD`)_, `UHT_API_KEY` _(for semantic lint)_ | `~/.airgenrc`     | API URL is `https://api.airgen.studio/api` in production.             |
| `@derive-ltd/reify`           | `REIFY_API_URL`, `REIFY_API_TOKEN`                                     | —                 | Tokens are generated server-side; the plaintext is shown once.        |
| `@derive-ltd/uht-substrate`   | `UHT_API_URL`, `UHT_TOKEN` _(or `UHT_API_KEY`)_                       | `uht-substrate config set …` | OAuth-issued token via the substrate web UI.                          |

## Quick command tour

### `@derive-ltd/airgen-cli` — requirements engineering

```sh
airgen tenants list
airgen reqs list <tenant> <project>
airgen reqs create <tenant> <project> --text "The system shall ..."
airgen baselines create <tenant> <project> --name "v1.0"
airgen diff <tenant> <project> --from <bl1> --to <bl2>
airgen lint <tenant> <project>           # semantic lint via UHT
airgen report compliance <tenant> <project>
```

Most commands take `<tenant> <project>` as positional arguments.
Append `--json` to any command for machine-readable output.

### `@derive-ltd/reify` — SysML model exploration

```sh
export REIFY_API_URL=https://reify.airgen.studio
export REIFY_API_TOKEN=rfy_...

reify list-projects
reify get-project <slug>
reify get-diagram <slug> --type=bdd --scope="Power Subsystem"
reify list-tools                          # introspect the live MCP surface
```

The package ships two binaries:

- `reify` — CLI that calls the remote `/mcp` endpoint
- `reify-mcp` — local stdio MCP server for Claude Desktop

CLI command names mirror MCP tool names — new server-side tools appear
in `reify list-tools` with no client changes.

### `@derive-ltd/uht-substrate` — semantic taxonomy

```sh
uht-substrate config set token <your-token>
uht-substrate classify hammer --format pretty
uht-substrate compare hammer screwdriver
uht-substrate facts store "spark plug" PART_OF "engine" --namespace SE:automotive
uht-substrate facts reconcile --namespace SE:my-project   # cross-check vs AIRGen
uht-substrate impact --airgen-diff diff.json --format pretty
```

## Wiring into Claude Desktop

For paste-ready Claude Desktop configurations, see the
[MCP tour](./mcp-tour.md). Summary:

| Component       | MCP transport                                                       |
| --------------- | ------------------------------------------------------------------- |
| Reify           | stdio via `npx -y @derive-ltd/reify`                                |
| AIRGen          | stdio via `npx -y -p @derive-ltd/airgen-cli airgen-mcp` (since 1.1.0) |
| UHT Substrate   | hosted streamable-http at `https://substrate.universalhex.org/mcp`  |

## Compatibility

The three packages talk to independent backends and can be used in
isolation. The natural pairings:

- **`@derive-ltd/airgen-cli` + `@derive-ltd/uht-substrate`** — `airgen
  lint` calls the substrate to classify domain concepts and flag
  ontological issues. Set `UHT_API_KEY` (or `UHT_TOKEN`) in addition
  to your AIRGen credentials.
- **`@derive-ltd/airgen-cli` + `@derive-ltd/uht-substrate impact`** —
  pipe `airgen diff --json` into `uht-substrate impact` to detect
  semantic drift between baselines.
- **`@derive-ltd/reify`** — read-only by design. Reads project data
  via the Reify HTTP API; the underlying data is sourced from AIRGen
  and the substrate.

## Versioning

| Package                       | Version line | Notes                                                                          |
| ----------------------------- | ------------ | ------------------------------------------------------------------------------ |
| `@derive-ltd/airgen-cli`      | 1.x          | Started shipping the bundled MCP server (`airgen-mcp` bin) at 1.1.0.           |
| `@derive-ltd/reify`           | 1.x          | Two bins from 1.0; tracks the Reify v1 HTTP API.                               |
| `@derive-ltd/uht-substrate`   | 1.x          | CLI client; MCP wiring is via the hosted endpoint, not bundled in the package. |

The packages are versioned independently; there is no monorepo
release lock-step. Pin specific versions in production wirings to
avoid surprise behavioural changes:

```sh
npx -y @derive-ltd/reify@1.1.0 list-projects
```

## Further reading

- [MCP tour — ten things to ask Claude](./mcp-tour.md)
- [Ecosystem overview](./overview.md)
- [AIRGen documentation](../airgen/)
- [Reify documentation](../reify/)
- [UHT Substrate documentation](../uht-substrate/)
