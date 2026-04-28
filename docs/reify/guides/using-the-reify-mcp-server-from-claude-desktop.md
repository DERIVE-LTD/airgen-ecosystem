# Using the Reify MCP server from Claude Desktop

`@derive-ltd/reify` ships a stdio MCP server (`reify-mcp`) that
exposes Reify's read-only tools to Claude Desktop, Claude Code, and
any other MCP-capable client. Wiring it in takes about a minute.

> **Prerequisites:**
>
> - Node.js ≥ 18.
> - A Reify Bearer token for a user with access to your projects.
>   Generate one with the Reify server's
>   `scripts/generate-api-token.ts` script.
> - Claude Desktop on macOS, Windows, or Linux.

## 1. Install (or just rely on `npx`)

For zero install, Claude Desktop can fetch the package at launch:

```json
{ "command": "npx", "args": ["-y", "@derive-ltd/reify"] }
```

For a slightly snappier launch and to pin a version:

```sh
npm install -g @derive-ltd/reify
```

This puts two binaries on your PATH:

- `reify-mcp` — the MCP server (stdio transport).
- `reify` — a CLI client that talks to the remote `/mcp` endpoint.

## 2. Configure Claude Desktop

Open the config:

| OS      | Path                                                              |
| ------- | ----------------------------------------------------------------- |
| macOS   | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Windows | `%APPDATA%\Claude\claude_desktop_config.json`                     |
| Linux   | `~/.config/Claude/claude_desktop_config.json`                     |

Add the Reify entry:

```json
{
  "mcpServers": {
    "reify": {
      "command": "npx",
      "args": ["-y", "@derive-ltd/reify"],
      "env": {
        "REIFY_API_URL": "https://reify.airgen.studio",
        "REIFY_API_TOKEN": "rfy_paste_your_token_here"
      }
    }
  }
}
```

For a self-hosted Reify, swap `REIFY_API_URL` for your domain.

## 3. Restart and verify

Quit Claude Desktop fully and re-open. The tool picker should now
include `reify` with ~15 stdio tools. A quick smoke test:
*"What Reify projects do I have access to?"* — Claude should call
`reify_list_projects` and return a list.

## 4. The 15 stdio tools

The bundled stdio server exposes a subset of the deployed `/mcp`
endpoint. Currently:

| Tool                            | What it does                                          |
| ------------------------------- | ----------------------------------------------------- |
| `reify_list_projects`           | List projects the token can access.                   |
| `reify_get_project`             | Project detail + workspace state.                     |
| `reify_get_stats`               | Fact / req / link / view counts by type.              |
| `reify_get_sysml`               | Full SysML (canonical or workspace).                  |
| `reify_list_sysml_sections`     | Section index.                                        |
| `reify_get_sysml_section`       | Body of one section.                                  |
| `reify_list_views`              | All views (optional viewpoint filter).                |
| `reify_get_view_sysml`          | SysML scoped to one view.                             |
| `reify_get_view_diagram`        | Structured diagram JSON for a view.                   |
| `reify_get_diagram`             | Structured diagram JSON by type.                      |
| `reify_list_requirements`       | AIRGen requirements (passthrough).                    |
| `reify_list_trace_links`        | AIRGen trace links (passthrough).                     |
| `reify_query_facts`             | Raw substrate fact query.                             |
| `reify_workspace_state`         | Editor workspace state + drift flag.                  |
| `reify_parse_sysml`             | Stateless SysML parser.                               |

The deployed `/mcp` endpoint additionally exposes
`reify_get_agent_session`, `reify_list_agent_sessions`,
`reify_get_checklist`, and `reify_get_project_ast`. Reach those via
the `reify` CLI (which talks to `/mcp` directly) until the stdio
server's tool list is synced.

## 5. Useful prompts

Once wired, ask Claude things like:

- *"Summarise the EEG Accessibility Buggy project — its subsystems,
  hazards, and requirement count."*
- *"Show me the Power Subsystem BDD view as a diagram."*
- *"Which trace links touch SYS-REQ-001?"*
- *"Parse this SysML snippet and tell me what substrate facts it
  would produce."*
- *"Is there an uncommitted workspace for `widget-x`, and has it
  drifted?"*

For cross-server queries (Reify + AIRGen + UHT Substrate), see
[the MCP tour](../../ecosystem/mcp-tour.md).

## 6. Run it locally for development

If you're hacking on the Reify MCP server itself:

```sh
cd packages/reify-mcp
npm run build
REIFY_API_URL=https://reify.airgen.studio \
REIFY_API_TOKEN=rfy_... \
node dist/index.js
```

The server speaks MCP over stdio — so it's not interactive. Pair it
with the [MCP Inspector](https://github.com/modelcontextprotocol/inspector)
to see tool calls in real time during development.

## All writes stay behind the UI

Reify's MCP and v1 HTTP API are deliberately read-only. Commit,
workspace edits, view CRUD, and agent triggers all live behind the
cookie-authenticated UI routes. There is no path to mutate state via
Bearer tokens — even if you wanted to.

This is by design: writes need the human-in-the-loop UI gate, and
agentic tools shouldn't bypass it.

## Troubleshooting

### "REIFY_API_URL is not set"

The env var didn't reach the MCP server. In Claude Desktop config,
both `REIFY_API_URL` and `REIFY_API_TOKEN` must live inside the
`env` object on the server entry, not at the top level of the JSON.

### `401 Unauthorized` on tool calls

The token is wrong, was revoked, or expired. Generate a fresh token
on the Reify server.

### `403 Forbidden` on tool calls

The token is valid but the user doesn't have access to the
requested project. Update `permissions.json` on the Reify server (or
ask whoever runs your Reify instance to grant access).

### Tools don't appear in Claude Desktop

Check the logs:

- macOS: `~/Library/Logs/Claude/mcp-*.log`
- Windows: `%APPDATA%\Claude\logs\mcp-*.log`

A common cause is `npx` not being on PATH from inside Claude
Desktop's launch environment. Use an absolute path:

```json
{ "command": "/usr/local/bin/npx", "args": ["-y", "@derive-ltd/reify"] }
```

## What's next

- [The MCP tour: ten things to ask Claude](../../ecosystem/mcp-tour.md)
- [Using the Reify HTTP API](./using-the-reify-http-api.md)
- [Embedding `sysml-reactflow` in your own application](./embedding-sysml-reactflow-in-your-own-application.md)
