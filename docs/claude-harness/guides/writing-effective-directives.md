# Writing effective directives for the autonomous loop

A directive is a free-text instruction the harness incorporates into
the next session's context. Directives are how operators steer the
loop without micro-managing each session. A well-written directive
saves dozens of cycles; a vague one wastes them.

This guide covers the structure of effective directives, common
patterns, and the anti-patterns that produce poor results.

> **Prerequisites:** ability to submit directives to a project's
> loop. In Derive, this is admin role. See
> [Driving the autonomous loop](../../derive/guides/driving-the-autonomous-loop.md)
> for the mechanics.

## The shape of a good directive

A useful directive has four parts:

1. **Focus.** What should the next session work on?
2. **Constraint.** What should it *not* do?
3. **Acceptance criterion.** How will the loop / operator know it's
   done?
4. **Context.** Anything relevant the loop wouldn't otherwise infer.

Worked example:

> *"Resolve orphan trace links in `system-requirements` (focus). Do
> not create new requirements (constraint). Acceptance: every
> requirement in that document has at least one incoming and one
> outgoing trace link, or is marked as intentionally untraced
> (criterion). Note: the parent layer is `stakeholder-requirements`;
> when proposing a parent, prefer existing stakeholder needs over
> creating new ones (context)."*

Compare to a bad directive:

> *"Clean up the project."*

The first version produces a focused session that finishes the work.
The second produces an unfocused session that does a little of
everything and finishes nothing.

## Patterns by phase

Different pipeline phases benefit from different directive styles.

### Concept / scaffold

Use directives to set scope and the structuring choices:

- *"This project is a SIL-2 control system for a marine pump. Build
  the document structure with stakeholder-requirements,
  system-requirements, sub-system-requirements (electrical,
  mechanical, control), interface-requirements, and
  hazard-analysis."*

- *"Use a four-layer decomposition: stakeholder → system →
  sub-system → component. Don't go deeper unless explicitly
  needed."*

### Decompose

Use directives to focus on one subtree at a time:

- *"Decompose stakeholder need STK-007 into system requirements.
  Aim for 5–10 system requirements; don't over-decompose. Use EARS
  patterns where the requirement has a clear trigger or state."*

- *"For each system requirement created, propose at least one
  verification activity, with method (Test / Analysis / Inspection
  / Demonstration) and a one-line description."*

### QC / quality polish

Use directives to lift specific QA dimensions:

- *"Polish QA on `system-requirements`. Target: median QA score
  ≥ 80. Don't change requirement intent — only wording. If a
  requirement is fundamentally weak, flag it for human review
  rather than rewriting."*

- *"For every requirement with a QA score below 60, propose a
  rewrite that lifts the score. Do not commit; create candidates
  for human review."*

### Validate / review

Use directives to focus the adversarial pass:

- *"Red-team the safety analysis for hazard HAZ-003. Look for
  unmitigated paths, single-point failures, and missing assumptions.
  Produce a list of concerns with severity, not requirement
  changes."*

- *"Validate the substrate facts under `SE:widget-x`. For each
  fact, check that subject and object exist as classified entities,
  and that the predicate matches the standard vocabulary. Flag
  inconsistencies."*

### Technical author

Use directives to shape voice, audience, and output:

- *"Polish the requirements text for an audience of mechanical
  engineers unfamiliar with the project. Active voice. UK English.
  Don't change meaning."*

- *"Generate a one-page executive summary of the project status,
  written for a non-technical project sponsor. 250 words."*

## Patterns that work

### Anchor to references

Always anchor to specific requirements, hazards, or sections by
reference. The loop has access to the references; vague descriptions
("the thermal stuff") force it to guess.

### Bound the work

Specify a target — *"5–10 requirements"*, *"median QA ≥ 80"*,
*"resolve at least 20 orphans"* — so the loop knows when to stop.
Without a bound, sessions sprawl.

### Prefer "do" over "don't"

Positive directives are easier for the loop to act on. Instead of
*"don't write composite requirements"*, say *"each requirement
should commit to one behaviour"*.

### Stack directives

Multiple directives stack. The loop reads the most recent ones
first. Use this for refinement:

- Day 1: *"Decompose stakeholder needs into system requirements."*
- Day 3: *"Polish QA on the system requirements created in the last
  three sessions; don't add more."*
- Day 7: *"Add verification activities for system requirements that
  don't yet have one."*

Each directive narrows the focus. The loop doesn't lose track of the
earlier ones — they're still in the project's directive history —
but it acts on the freshest signal.

### Match directive to gate

A directive that asks for output the gates won't accept produces a
loop that fails repeatedly. If your QA gate is 70 and you direct
*"draft requirements at any QA level"*, expect the gate to reject
the output. Either lower the gate (in workflow config) or raise the
directive's bar.

## Anti-patterns

### "Improve the project"

Too vague. The loop has no measurable target, no scope. Wastes a
session.

### "Fix everything"

Same. Worse, the loop will try, succeed at nothing, and produce a
demoralising journal post.

### "Make it like project X"

The loop doesn't have cross-project context (sessions are scoped to
one project's namespace). This will fail.

### "Just decompose"

Marginal. The loop will pick a stakeholder need at random and
decompose it; the result is noisy and unfocused. Always anchor to a
specific stakeholder need or document.

### Directive overload

A 500-word directive with eight conflicting goals is harder to act
on than three crisp 100-word directives stacked over a week.

### Tone instructions only

*"Be more formal."* The loop needs *what* to do, not *how* to do
it. If voice matters, fold it into a focused directive: *"Polish
the executive summary of widget-x for a regulator audience —
formal, third-person, UK English."*

## Reviewing directive output

After a directive-driven session lands:

1. **Read the journal post.** Does the loop's understanding of the
   directive match yours?
2. **Walk the candidates in AIRGen.** Are the changes what you
   asked for?
3. **Check the gate state.** Did the session pass its gates?

If the output doesn't match the directive, two reasons usually
apply:

- The directive was ambiguous. Refine and submit again.
- The loop ran out of turns. Either continue with a follow-up
  directive ("continue from where you left off, focusing on…") or
  raise the turn budget.

## Worked example: a week of directives

```
Mon  "Decompose STK-001 through STK-005 into system-requirements
      under document `system-requirements`. 5–10 system requirements
      per stakeholder need. Use EARS where applicable. Propose at
      least one verification activity per system requirement."

Wed  "Polish QA on the system-requirements created Mon–Tue. Target
      median QA score 80. Wording-only changes; flag any requirement
      that needs structural rework."

Thu  "Validate substrate facts for the new system-requirements. Run
      reconcile; surface any drift. Don't fix automatically — flag
      for review."

Fri  "Generate a one-page status summary for the project sponsor
      covering this week's decomposition activity. 250 words.
      Non-technical audience."
```

A focused week. Each directive has a clear scope, target, and
audience. The loop produces useful work; the operator reviews it
once a day.

## What's next

- [Understanding a harness session](./understanding-a-harness-session.md)
- [Reading session journals](./reading-session-journals.md)
- [Driving the autonomous loop (Derive)](../../derive/guides/driving-the-autonomous-loop.md)
