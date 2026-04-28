# Getting started with Derive

Derive is the autonomous workbench layered on top of [AIRGen](../airgen/).
This guide walks through running your first agent flow against a project.

> **Prerequisites:**
>
> - An AIRGen account ([airgen.studio](https://airgen.studio) or self-hosted)
> - A project in AIRGen with at least one stakeholder requirement
> - A Derive account at [derive.airgen.studio](https://derive.airgen.studio)

## 1. Connect Derive to your AIRGen tenant

_TODO: walkthrough — selecting the AIRGen tenant, providing API
credentials, confirming the connection._

## 2. Open a project in Derive

Derive lists projects from the connected AIRGen tenant. Open one to see
its current requirements, trace links, and previous agent sessions.

_TODO: screenshot of the project view._

## 3. Run a decomposition flow

From the project page, choose **Run flow → Decompose**. Derive launches
an agent session that:

1. Reads the stakeholder requirement(s) and any existing context
2. Proposes system-level requirements with rationale
3. Suggests trace links between the new requirements and the source
4. Writes the proposed artefacts back to AIRGen as **candidates**

_TODO: screenshot of a running session with live progress._

## 4. Review and accept candidates

All agent output lands in AIRGen as candidates — you can accept,
reject, or edit each item before it becomes part of the live data set.
This human-in-the-loop gate is intentional: in regulated contexts,
agent-drafted artefacts must be reviewed by a competent engineer.

_TODO: screenshot of the candidate review UI._

## 5. Generate a report

Once you have a tidy set of accepted requirements and trace links,
choose **Generate report** to produce a structured engineering report.
Reports are versioned alongside the project.

## Next steps

- _TODO: link to "Understanding agent flows" guide_
- _TODO: link to "Custom flow definitions" guide_
- _TODO: link to ecosystem overview_
