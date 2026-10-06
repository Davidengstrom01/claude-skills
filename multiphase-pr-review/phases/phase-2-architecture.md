# Phase 2 — Assess the architecture

## 2.1 — Describe the current design

Trace the main flows through the affected parts of the system. Identify who owns each rule, piece of data, and side effect.

**Output:** A concise account of how the design works.

## 2.2 — Check the design against the requirements

Assess whether ownership, dependencies, and boundaries support the behavior established in phase 1.

**Output:** Supported design choices and candidate concerns.

## 2.3 — Examine relevant failure paths

Check what happens when operations fail, repeat, overlap, or finish only partly. Include these scenarios where the change makes them relevant.

**Output:** Failure scenarios and the guarantees the design provides or lacks.

## 2.4 — Consider simpler alternatives

For each material design concern, describe a simpler approach that meets the same requirements. Explain the benefit, cost, and effect on scope.

**Output:** Concrete alternatives, rather than general requests to simplify.

## 2.5 — Prepare the architecture grilling session

Collect unresolved design choices for the checkpoint grilling session. Ask now only when an answer blocks the rest of this phase.

Present unresolved design choices with evidence, options, and a recommendation. Test answers against concrete scenarios. Record the decisions and examine any new consequences.

Repeat until each material choice has a stated rationale or remains explicitly unresolved.

**Output:** Design decisions, accepted tradeoffs, and unresolved questions.

## 2.6 — Save the architecture assessment

Classify each concern as a finding, accepted tradeoff, follow-up, or unresolved question.

### Phase 2 is complete when

- The main flows and relevant failure paths are understood.
- Material design choices have evidence or a stated rationale.
- Each concern has a recorded disposition.
- No unresolved design question prevents further assessment.

The phase may conclude that the architecture needs changes.

**Next:** Hold the phase checkpoint described in SKILL.md. Start phase 3 only after the user says to continue.
