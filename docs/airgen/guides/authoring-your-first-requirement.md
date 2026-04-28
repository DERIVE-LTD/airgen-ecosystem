# Authoring your first requirement

This guide walks through writing one good requirement from a blank
page in AIRGen — from picking the right form, through the QA
feedback, to attaching it to a document.

> **Prerequisites:** an account on
> [airgen.studio](https://airgen.studio) (or a self-hosted instance)
> and a project to work in. If you don't have one, see the
> [getting-started guide](../getting-started.md).

## What "good" looks like

A requirement in AIRGen is a single sentence that describes one
testable behaviour or constraint of the system. The QA engine scores
each requirement 0–100 against ISO/IEC/IEEE 29148 and EARS pattern
rules. Three quick litmus tests:

1. **Singular.** Does this say *one* thing? If you find yourself
   writing "and" or "as well as", split it.
2. **Testable.** Could a verification engineer write a Pass/Fail
   procedure from this sentence alone?
3. **Bounded.** Is the trigger, the precondition, and the response
   all clearly stated?

The EARS family of patterns is a useful scaffold:

| Pattern         | Template                                                      |
| --------------- | ------------------------------------------------------------- |
| **Ubiquitous**  | The `<system>` shall `<response>`.                            |
| **Event-driven**| When `<trigger>`, the `<system>` shall `<response>`.          |
| **State-driven**| While `<state>`, the `<system>` shall `<response>`.           |
| **Unwanted**    | If `<unwanted condition>`, then the `<system>` shall `<response>`. |
| **Optional**    | Where `<feature>`, the `<system>` shall `<response>`.         |

For more on the patterns, see
[Understanding the QA score and EARS patterns](./understanding-the-qa-score-and-ears-patterns.md).

## 1. Pick a document

Requirements live inside documents. Documents are typed (system
requirements, sub-system requirements, interface requirements, …)
and ordered. Open the project, navigate to **Documents**, and either:

- Open an existing document (e.g. `system-requirements`), or
- Create one with **Documents → New** if your project is empty.

If you don't yet have a document structure in mind, a minimal split
is `stakeholder-requirements`, `system-requirements`, and
`interface-requirements`.

## 2. Write the requirement

In the document view, **Requirements → New** opens the editor. A good
first requirement, event-driven pattern:

> **When** the operator presses the EMERGENCY STOP button, **the
> control system shall** transition to the SAFE state within 100 ms.

Type it into the body field, then save. AIRGen runs the deterministic
QA pipeline immediately and assigns a score.

## 3. Read the QA panel

The right-hand panel breaks down the score by rule. Common feedback
on a first attempt:

- **Pattern detected.** AIRGen recognises this as event-driven and
  marks the trigger / response cleanly.
- **Modal verb.** Good — "shall" is the canonical commitment word.
- **Quantification.** "Within 100 ms" is measurable.
- **Ambiguity.** No vague terms (avoid "appropriate", "reasonable",
  "as required").
- **Composite.** Single behaviour, no compound demands.

If a rule fails, the panel suggests an improvement. Apply the
suggestion (or ignore it with a justification — sometimes the rule
isn't the right judgement) and save again.

## 4. Tag and section the requirement

Tags and section assignment are how requirements become navigable.
Useful tag conventions:

- **Functional area.** `safety`, `power`, `comms`, `nav`, …
- **Origin.** `stakeholder`, `derived`, `imported`, …
- **Lifecycle.** `draft`, `reviewed`, `frozen`.
- **Verification method.** `test`, `analysis`, `inspection`,
  `demonstration` (matches the TADI taxonomy in AIRGen verification).

Sections live within documents and are how you give a requirement a
position in the document tree (e.g. "3.2.1 Emergency stop").

## 5. Add the first trace link

A requirement that doesn't trace anywhere is a candidate orphan. Pick
a parent — typically a higher-level stakeholder need — and link:

- Open the parent requirement (in `stakeholder-requirements`).
- Use **Trace → New** with the new requirement as **target** and the
  stakeholder requirement as **source**, link type `derives`.

The graph database means this link is now navigable in both
directions. AIRGen's **Traceability** view shows the full chain.

## 6. Record verification intent

Even at draft stage, declare how this requirement will be verified.
In **Verification → New activity**:

- **Method:** Test (or Analysis / Inspection / Demonstration).
- **Title:** "Emergency stop response time test."
- **Linked requirement:** the new requirement.

This is how AIRGen's verification engine later knows to chase
evidence for this requirement.

## Common patterns

- **Author in passes.** First pass: get the behaviour down.
  Second pass: tighten language to lift the QA score. Third pass:
  trace and assign verification.
- **Use the AI draft for stuck cases.** `airgen qa draft "user
  needs..."` (CLI) or the Imagine / draft features in the web UI
  produce candidates you can refine. Treat them as sketches.
- **Score the project periodically.** Run the background QA worker
  (`airgen qa score start <tenant> <project>`) before reviews — it
  re-scores everything, including older requirements, against the
  current rule set.

## What's next

- [Understanding the QA score and EARS patterns](./understanding-the-qa-score-and-ears-patterns.md)
- [Building a traceability matrix](./building-a-traceability-matrix.md)
- [Using the AIRGen CLI](./using-the-airgen-cli.md)
