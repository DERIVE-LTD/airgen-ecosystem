# Connecting the substrate MCP server to Claude Desktop

UHT Substrate exposes its tools over MCP at the hosted endpoint
`https://substrate.universalhex.org/mcp`. Wiring it into Claude
Desktop takes about a minute and gives Claude access to
classification, fact, entity, and namespace tools without leaving the
chat.

> **Prerequisites:**
>
> - A substrate Bearer token. Sign in with Google or GitHub at
>   [substrate.universalhex.org](https://substrate.universalhex.org)
>   to provision one.
> - Claude Desktop on macOS, Windows, or Linux. Older versions may
>   need an HTTP-stdio bridge — see the troubleshooting section.

## 1. Get a token

Sign in to [substrate.universalhex.org](https://substrate.universalhex.org)
and copy your Bearer token from the user / API page. Tokens are
sha256-hashed at rest; the plaintext is only shown to you, so save it
somewhere private.

## 2. Locate your Claude Desktop config

| OS      | Path                                                                    |
| ------- | ----------------------------------------------------------------------- |
| macOS   | `~/Library/Application Support/Claude/claude_desktop_config.json`       |
| Windows | `%APPDATA%\Claude\claude_desktop_config.json`                           |
| Linux   | `~/.config/Claude/claude_desktop_config.json`                           |

If the file doesn't exist, create it with `{ "mcpServers": {} }`.

## 3. Add the substrate entry

The canonical config uses HTTP-streamable transport — no local install
needed:

```json
{
  "mcpServers": {
    "uht-substrate": {
      "url": "https://substrate.universalhex.org/mcp",
      "transport": "streamable-http",
      "headers": {
        "Authorization": "Bearer YOUR_TOKEN_HERE"
      }
    }
  }
}
```

Substitute your token directly into the header (Claude Desktop
doesn't expand `${UHT_TOKEN}` from the shell environment in this
position).

## 4. Restart Claude Desktop and verify

Quit Claude Desktop fully and re-open. The tool picker should now
include `uht-substrate` with classification, entity, fact, and
namespace tools.

A quick smoke test: ask Claude *"What namespaces are available in UHT
Substrate?"* — Claude should call `list_namespaces` and return a
list. If it does, the wiring is good.

## 5. Useful prompts

Once wired, the substrate becomes a reasoning surface inside any
conversation:

- *"Classify these 20 components into the UHT taxonomy and return
  them as a markdown table sorted by similarity to a hammer."*
- *"For namespace `SE:widget-x`, list every entity that has a
  `HAS_HAZARD` fact, and group by hazard severity."*
- *"Compare 'cognitive dissonance' to 'epistemic humility' and
  explain the trait differences in plain language."*
- *"Reconcile substrate facts against AIRGen trace links for
  project widget-x and summarise the top 5 issues."*

Pair this with the AIRGen and Reify MCP servers (see
[the MCP tour](../../ecosystem/mcp-tour.md)) for cross-server
queries.

## Troubleshooting

### "Tool server failed to start"

Most likely your Claude Desktop version doesn't support
`streamable-http` transports natively. Use an HTTP-stdio bridge such
as `mcp-remote`:

```json
{
  "mcpServers": {
    "uht-substrate": {
      "command": "npx",
      "args": [
        "-y", "mcp-remote",
        "https://substrate.universalhex.org/mcp",
        "--header", "Authorization: Bearer YOUR_TOKEN_HERE"
      ]
    }
  }
}
```

`mcp-remote` runs as a stdio MCP server locally and forwards calls
over HTTP to the hosted endpoint.

### "Tool calls return 401 Unauthorized"

The token is wrong, expired, or revoked. Sign in again at
[substrate.universalhex.org](https://substrate.universalhex.org) and
generate a fresh one.

### "Tool calls return 403 Forbidden"

The token is valid but doesn't have access to the namespace you're
querying. Namespaces are per-account; ask the namespace owner to
grant access.

### "Tools don't appear in the picker"

Check the Claude Desktop logs:

- macOS: `~/Library/Logs/Claude/mcp-*.log`
- Windows: `%APPDATA%\Claude\logs\mcp-*.log`

Common causes: malformed JSON in `claude_desktop_config.json`, a
missing trailing comma, or the entry placed outside the `mcpServers`
object.

## What's next

- [The MCP tour: ten things to ask Claude](../../ecosystem/mcp-tour.md)
- [Classifying entities for a new project](./classifying-entities-for-a-new-project.md)
