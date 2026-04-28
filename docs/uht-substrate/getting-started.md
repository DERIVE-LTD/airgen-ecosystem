# Getting started with UHT Substrate

UHT Substrate is the semantic-reasoning engine and fact store behind
the AIRGen ecosystem. This guide walks through your first classification,
comparison, and stored fact.

> **Prerequisites:**
>
> - An account on [substrate.universalhex.org](https://substrate.universalhex.org)
>   and a Bearer token (sign in with Google or GitHub to provision one),
>   or a self-hosted Substrate instance you can authenticate against.

## 1. Install the CLI

```sh
npm install -g @derive-ltd/uht-substrate
uht-substrate config set api-url https://substrate.universalhex.org/api
uht-substrate config set token YOUR_TOKEN
```

(REST API and MCP server work too — see the [Substrate README](./README.md);
this guide uses the CLI for brevity.)

## 2. Classify your first entity

```sh
uht-substrate classify "hammer" --format pretty
```

The response includes:

- An 8-character hex code (the trait vector)
- The 32 individual traits, grouped by layer
- A short description and disambiguation if relevant

_TODO: example output block._

## 3. Compare two entities

```sh
uht-substrate compare hammer screwdriver
```

You get a Jaccard similarity score and a breakdown of which traits the
two entities share and which differ.

## 4. Search the corpus

```sh
uht-substrate search "hand tools" --limit 10
```

Returns the closest matches in the Factory entity corpus, ranked by
trait overlap.

## 5. Store a fact

Facts are subject-predicate-object triples scoped to a **namespace**.
For systems-engineering projects, the convention is `SE:<project-slug>`:

```sh
uht-substrate facts store "spark plug" PART_OF "engine" \
  --namespace SE:my-project
```

You can then query the namespace's facts:

```sh
uht-substrate facts namespace-context SE:my-project
uht-substrate facts query --namespace SE:my-project --predicate PART_OF
```

## 6. Reconcile against AIRGen

If your project also uses AIRGen, you can cross-check substrate facts
against AIRGen trace links to find inconsistencies between the two:

```sh
# (CLI tool: facts reconcile — confirm exact name in your CLI version)
uht-substrate facts reconcile --namespace SE:my-project
```

## Next steps

- _TODO: link to "Storing and querying facts with namespace scoping"_
- _TODO: link to "Reconciling substrate facts against AIRGen trace links"_
- _TODO: link to "Connecting the substrate MCP server to Claude Desktop"_
- _TODO: link to "Self-hosting UHT Substrate with Neo4j"_
