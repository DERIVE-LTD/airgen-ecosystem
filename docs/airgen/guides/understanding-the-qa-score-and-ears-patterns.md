# Understanding the QA score and EARS patterns

AIRGen's QA engine assigns every requirement a 0–100 score against
deterministic rules drawn from ISO/IEC/IEEE 29148 (the requirements
engineering standard) and the EARS family of patterns. The score
isn't a grade in itself; it's a structured way of pointing at the
specific weaknesses in a requirement.

This guide explains where the score comes from, how to read each rule
category, and how to drive scores up without gaming them.

## The score in one paragraph

The score is a weighted sum across rule categories, normalised to 0–
100. Failing rules deduct points; passing rules contribute the
maximum. The engine is deterministic — the same text produces the
same score every time. There is no LLM judgment in the score itself;
the AI features (drafting, suggestions) are layered on top of the
score, not part of it.

## The five EARS patterns

EARS — Easy Approach to Requirements Syntax — defines five canonical
templates. Most requirements you write should match one of them.

### Ubiquitous

```
The <system> shall <response>.
```

- *The thermal controller shall maintain payload temperature between 0 °C and 40 °C.*

Use for invariants and ongoing behaviours.

### Event-driven

```
When <trigger>, the <system> shall <response>.
```

- *When the operator presses the STOP button, the controller shall halt motion within 100 ms.*

Use for behaviours triggered by an external event.

### State-driven

```
While <state>, the <system> shall <response>.
```

- *While the system is in MANUAL mode, the controller shall ignore automated set-points.*

Use for behaviours that hold while the system is in some mode.

### Unwanted

```
If <unwanted condition>, then the <system> shall <response>.
```

- *If the coolant temperature exceeds 90 °C, then the controller shall enter SAFE mode.*

Use for fault-handling and contingency behaviours.

### Optional

```
Where <feature>, the <system> shall <response>.
```

- *Where the optional logging board is fitted, the controller shall write a 1 Hz telemetry stream.*

Use for behaviours conditional on a configurable feature or option.

AIRGen detects which pattern your requirement uses (or flags
"ambiguous") in the QA panel.

## The rule categories

Each rule sits in one of these categories:

### Modal verbs

The standard requires `shall` for binding requirements. AIRGen warns
about:

- **Forbidden modals:** `should`, `may`, `will`, `can`, `must`.
- **Negative modals:** `shall not` is fine but often better expressed
  as a positive constraint.

### Quantification

Numeric thresholds with units. Watch for:

- Bare numbers without units (`100` vs `100 ms`).
- Vague quantifiers (`approximately`, `roughly`, `near`).
- Rates without a measurement window (`fast` vs `at 1 Hz`).

### Ambiguity

Words that mean different things to different readers:

- *appropriate, reasonable, sufficient, adequate, normal, typical*

The fix is almost always to substitute a measurable criterion.

### Composite / atomicity

A requirement should commit to one behaviour. Triggers for the
composite check:

- "and" or "as well as" linking two responses
- Bullet-pointed responses inside one requirement
- Multiple verbs in the response clause

Split composite requirements into atoms with parent–child trace
links.

### Verifiability

Even a perfectly worded requirement is useless if it can't be
verified. The engine flags:

- Internal-state references that have no external observable
  ("shall feel responsive")
- Behaviours predicated on unobservable conditions

### Length

Both ends are flagged. Too short ("the system shall work") usually
means under-specified; too long usually means composite. AIRGen has
calibrated thresholds — adjust by the suggestions, not by chasing the
length itself.

### Pattern compliance

The engine identifies which EARS pattern (if any) the requirement
matches and warns when:

- The trigger word is missing or wrong (`When` mistakenly used for a
  state-driven requirement)
- The order of trigger / response is reversed
- The pattern is ambiguous (multiple plausible classifications)

## Reading the score in context

A 100 isn't always achievable, and a 70 isn't always wrong. Two
patterns to watch for:

### Suppress noise on intentional choices

If the rule says "ambiguous: 'appropriate'" but you have a domain
glossary that defines "appropriate" precisely, the rule is firing on
a false positive. AIRGen lets you mark a rule as **suppressed** with
a justification — the score still penalises it, but the suppression
shows up in reviews so the choice is auditable.

### Trends matter more than absolutes

For a project with 500 requirements, the question isn't "which is at
100?" but "is the median rising as we polish?" Run the background QA
worker (`airgen qa score start`) before milestones and compare runs.

## Working with the score from the CLI

```sh
# Score one requirement on the fly
airgen qa analyze "When the operator presses STOP, the controller shall halt within 100 ms."

# Trigger a project-wide score
airgen qa score start <tenant> <project>

# Get a quality summary
airgen report quality <tenant> <project>

# List requirements below a threshold
airgen reqs filter <tenant> <project> --score-max 60
```

Treat the score as a sorting key for review work, not a grade for
the document.

## What's next

- [Authoring your first requirement](./authoring-your-first-requirement.md)
- [Building a traceability matrix](./building-a-traceability-matrix.md)
- [Using the AIRGen CLI](./using-the-airgen-cli.md)
