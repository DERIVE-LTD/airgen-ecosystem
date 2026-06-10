# airgen-ecosystem

Public, docs-only repository. Technical guides for the AIRGen platform —
five components, one pipeline. Licensed CC BY 4.0; the audience is
external readers (customers, integrators, the curious).

## What lives here vs. what doesn't

This repo has **no application code**. The source it documents lives in
the sibling working tree `/root/airgen-platform`, which is private. The
prod deploy of that tree is at `root@100.98.226.76:/opt/airgen-platform`
(Tailscale).

Read the source freely when writing docs — accuracy matters more than
isolation — but never copy private code, secrets, internal hostnames,
deploy-key paths, or unreleased roadmap items into a guide here.

## This directory is also a control centre

The user often runs platform deploy commands from this working tree even
though the deploys target `/root/airgen-platform` source files. That's
why `.claude/settings.local.json` is dense with `rsync`/`scp`/`ssh` and
`pnpm -C apps/...` entries that don't reference paths inside this repo.
Don't prune it for "looking unrelated" — those allowlist entries reduce
permission prompts when running deploys from here. See memory
`project-deploy-airgen-platform` for the actual deploy paths
(`scripts/deploy-flows.sh` for flow content, full
`docker compose --build` for code).

## Repository layout

```
docs/
├── airgen/          AIRGen (airgen.studio) — requirements platform
├── derive/          Derive (derive.airgen.studio) — operator workbench
├── reify/           Reify (reify.airgen.studio) — SysML v2 viewer/editor
├── uht-substrate/   UHT Substrate (substrate.universalhex.org) — fact store
├── claude-harness/  Claude Harness (operator-facing, source private)
└── ecosystem/       Cross-cutting: overview, MCP tour, npm packages
```

Each component dir has `README.md`, `getting-started.md`, and `guides/`.

## How docs are written here

The working pattern (visible in recent commits — "Restructure X guides
against live UI") is: **walk the live product, then write the doc**.
Don't infer UI from source code; UIs shift faster than guides do and
inference produces stale prose.

For UI-bearing guides (AIRGen, Derive, Reify):

1. Open the live product via Chrome DevTools MCP (`mcp__chrome-devtools__*`).
2. Walk the feature end-to-end; screenshot the actual screens.
3. Rewrite the guide against what you saw, not against the source tree.

For CLI / MCP / API guides: cross-check against `/root/airgen-platform`
source, then verify by running the command or hitting the endpoint.

The MCP servers for AIRGen, Derive, and Reify are already authenticated
in this session — use them for any guide that touches those products'
data model.

## Style

- Prose: British English, present tense, second person ("you do X").
- Markdown: GitHub-flavoured. Tables for component matrices, fenced
  blocks for commands, relative links between docs.
- No emoji. No marketing voice.
- When linking to source files, link to the upstream public package (e.g.
  `@derive-ltd/*` on npm) rather than `/root/airgen-platform` paths.

## What not to add

- GitHub Actions / CI. Builds happen locally before pushing.
- New top-level dirs without an explicit ask.
- Roadmap or "coming soon" content. Document what ships today.
- The `claude-harness` source is private; describe its operator surface
  but never imply that the source is publicly available.
