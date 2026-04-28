# Connecting AIRGen to Claude via the MCP server

`@derive-ltd/airgen-cli` v1.1.0+ ships a bundled MCP server (the
`airgen-mcp` binary) that exposes 70 AIRGen tools across 18 modules
to Claude Desktop, Claude Code, and the Claude.ai web connector.

This guide covers both transports — stdio (for desktop clients) and
HTTP (for the Claude.ai connector) — and the quick checks that
confirm everything is wired correctly.

> **Prerequisites:**
>
> - `@derive-ltd/airgen-cli` 1.1.0 or later installed.
> - An AIRGen API key (`ak_...`) or email + password.
> - Claude Desktop 0.7+ (or any MCP-capable client).

## Install

```sh
npm install -g @derive-ltd/airgen-cli
```

This puts both `airgen` (the CLI) and `airgen-mcp` (the MCP server)
on your PATH.

## Stdio — Claude Desktop and Claude Code

Open your Claude Desktop config:

| OS      | Path                                                              |
| ------- | ----------------------------------------------------------------- |
| macOS   | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Windows | `%APPDATA%\Claude\claude_desktop_config.json`                     |
| Linux   | `~/.config/Claude/claude_desktop_config.json`                     |

Add the AIRGen entry:

```json
{
  "mcpServers": {
    "airgen": {
      "command": "airgen-mcp",
      "env": {
        "AIRGEN_API_URL": "https://api.airgen.studio/api",
        "AIRGEN_API_KEY": "ak_paste_your_key_here"
      }
    }
  }
}
```

Restart Claude Desktop. The tool picker should now include
`airgen` with 70 tools.

If you'd rather avoid a global install, use the `npx` form:

```json
{
  "command": "npx",
  "args": ["-y", "-p", "@derive-ltd/airgen-cli", "airgen-mcp"]
}
```

Claude Desktop will fetch the package on demand.

## HTTP — the Claude.ai web connector

For the Claude.ai web app's connector model, run `airgen-mcp` as an
HTTP server. Set `MCP_PORT` and the server switches transports:

```sh
MCP_PORT=3100 \
AIRGEN_API_URL=https://api.airgen.studio/api \
AIRGEN_API_KEY=ak_... \
airgen-mcp
```

It will listen on `http://localhost:3100/mcp`. For real use, front
it with a TLS-terminating reverse proxy, restrict access (your API
key is the only auth on the upstream — the MCP layer doesn't add a
separate auth gate), and add the resulting URL as a connector at
[claude.ai](https://claude.ai) → Settings → Connectors.

A minimal Caddy fronting:

```
mcp.example.com {
    reverse_proxy localhost:3100
}
```

## Required environment variables

| Variable             | Required | Notes                                                            |
| -------------------- | -------- | ---------------------------------------------------------------- |
| `AIRGEN_API_URL`     | yes      | Base URL — `https://api.airgen.studio/api` in production. The `api.` subdomain is required. |
| `AIRGEN_API_KEY`     | yes*     | Bearer-style key starting `ak_...`. Preferred over login.        |
| `AIRGEN_EMAIL`       | yes*     | If `AIRGEN_API_KEY` is not set.                                  |
| `AIRGEN_PASSWORD`    | yes*     | If `AIRGEN_API_KEY` is not set.                                  |
| `MCP_PORT`           | no       | Set to switch from stdio to HTTP transport.                      |

*One of `AIRGEN_API_KEY` or `AIRGEN_EMAIL`+`AIRGEN_PASSWORD` is
required.

## Smoke test

In Claude Desktop, ask: *"What tenants do I have access to in
AIRGen?"*

Claude should call `airgen.tenants.list` (or whichever exact tool
name surfaces) and return the list. If it does, you're wired.

## The 70 tools, by module

The MCP server exposes its 70 tools in 18 modules:

| Module                | What you can ask Claude to do                                  |
| --------------------- | -------------------------------------------------------------- |
| `navigation`          | Walk projects, documents, sections.                            |
| `project-management`  | Tenants, projects CRUD.                                        |
| `requirements`        | List, search, filter, create, update, archive.                 |
| `documents`           | Document CRUD.                                                 |
| `document-management` | Folder structure, ordering, sections.                          |
| `section-content`     | Section-scoped requirement lists.                              |
| `quality`             | QA scoring (single requirement, project-wide).                 |
| `traceability`        | Trace links, linksets, coverage.                               |
| `baselines`           | Snapshots and diffs.                                           |
| `architecture`        | Diagrams, blocks, ports, connectors.                           |
| `search`              | Full-text and semantic search.                                 |
| `filtering`           | Multi-field filter compositions.                               |
| `ai`                  | AI-driven candidate generation, candidates inbox.              |
| `imagine`             | Concept image generation.                                      |
| `activity`            | Recent project activity feed.                                  |
| `implementation`      | Per-requirement implementation status and artefact links.      |
| `reporting`           | Stats, quality, compliance, orphans, exports.                  |
| `import-export`       | CSV / markdown / JSON in and out.                              |

Run `airgen-mcp --help` (or check the upstream README) for the
canonical tool list at the time of installation.

## Useful prompts to try

Once wired, AIRGen becomes a chat-driven surface. Start with:

- *"List the requirements in document `system-requirements` for
  `acme/widget-x`, sorted by QA score ascending. Show me the lowest
  five."*
- *"Find every requirement in `acme/widget-x` that has no incoming or
  outgoing trace links and group them by document."*
- *"Show me what changed between baselines `v1.0` and `v1.1` of
  `widget-x` and turn the result into a release-note bullet list."*
- *"Draft five candidate system requirements that satisfy this
  stakeholder need: 'Operators must be able to power-cycle the unit
  remotely without losing telemetry continuity.' Use EARS where
  appropriate."*

Pair AIRGen's MCP with Reify and UHT Substrate for cross-server
queries — see [the MCP tour](../../ecosystem/mcp-tour.md).

## Troubleshooting

### "AIRGEN_API_URL is required"

The env var didn't make it into the MCP server's environment. In
Claude Desktop, env vars must live inside the `env` object on the
server entry — not at the top level of the JSON.

### "401 Unauthorized" when running tool calls

Either:

- `AIRGEN_API_KEY` is wrong / revoked. Generate a fresh one from the
  AIRGen web UI (account → API keys).
- Email + password is wrong. Test with `airgen tenants list` from a
  shell with the same env vars; the CLI will use the same auth path
  and surface the error more clearly.

### Tools don't appear

Check the Claude Desktop log:

- macOS: `~/Library/Logs/Claude/mcp-*.log`
- Windows: `%APPDATA%\Claude\logs\mcp-*.log`

The most common cause is `airgen-mcp` not being on PATH from inside
Claude Desktop's launch environment. Use an absolute path:

```json
{ "command": "/usr/local/bin/airgen-mcp" }
```

or fall back to the npx form.

## What's next

- [The MCP tour: ten things to ask Claude](../../ecosystem/mcp-tour.md)
- [Using the AIRGen CLI](./using-the-airgen-cli.md)
- [Building a traceability matrix](./building-a-traceability-matrix.md)
