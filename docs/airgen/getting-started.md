# Getting started with AIRGen

This guide walks through your first 30 minutes with AIRGen — from sign-up
to your first traced, QA-scored requirement.

> **Prerequisites:** an account on [airgen.studio](https://airgen.studio),
> or a self-hosted instance you can sign in to.

## 1. Create a project

After signing in, create a new project from the dashboard. Projects
isolate requirements, documents, baselines, and trace links.

_TODO: screenshot of project creation._

## 2. Author your first requirement

Open the project and select **Requirements → New**. Enter a single
shall-statement, for example:

> The system shall transmit telemetry to the ground station at 1 Hz when
> in nominal operations mode.

Save it. AIRGen will run the deterministic QA pipeline and assign a
score from 0 to 100 against ISO/IEC/IEEE 29148 and EARS pattern rules.

_TODO: screenshot of the QA score panel and rule breakdown._

## 3. Review the QA score

Click into the requirement. The QA panel highlights:

- Which rules passed and which failed
- The detected EARS pattern (Ubiquitous, Event-driven, State-driven, etc.)
- Suggested improvements

Refine the wording until you reach a score you are happy with.

## 4. Add traceability

Open a second requirement (or create one) and link the two using a typed
trace link. AIRGen supports `satisfies`, `derives`, `verifies`,
`implements`, `refines`, and `conflicts`.

The graph database means you can navigate from any node in either
direction, and view the full chain of derivation.

## 5. Take a baseline

Once you have a stable set of requirements, create a **baseline** from
the project menu. Baselines are immutable snapshots — useful for release
gates, customer deliveries, and regulatory submissions.

Compare two baselines to see exactly what has changed between them.

## Next steps

- _TODO: link to "Authoring requirements that score well" guide_
- _TODO: link to "Using the `@derive-ltd/airgen-cli` CLI" guide_
- _TODO: link to "Connecting AIRGen to Claude via MCP" guide_

If you are evaluating self-hosted AIRGen, see _TODO: link to
"Self-hosting AIRGen with Docker Compose"_.
