# Phase 1 — Define the problem and review scope

## 1.1 — Identify the review target

Record the PR, base commit, and head commit. Note any explicit scope limits.

**Output:** The exact changes under review.

## 1.2 — Establish the intended behavior

Read the ticket, PR description, relevant domain rules, and prior decisions. Describe the problem, expected outcome, and acceptance criteria. Mark inferred requirements as assumptions.

**Output:** A clear account of what this PR should accomplish.

## 1.3 — Compare intent with implementation

Map each requirement to the relevant changes. Identify missing behavior, extra scope, and conflicts between the ticket and implementation.

**Output:** A requirement list with supporting code references and gaps.

## 1.4 — Check the wider context

Read affected callers, dependencies, and adjacent flows. Identify anything needed to assess the PR fully. Keep unrelated improvements outside the review scope.

**Output:** The areas to inspect and explicit exclusions.

## 1.5 — Prepare the scope grilling session

Collect unresolved intent or scope questions for the checkpoint grilling session. Ask now only when an answer blocks the rest of this phase.

Ask about unresolved intent or scope that the available sources cannot settle. Give a recommended answer and its reason. Record each answer, revisit the requirement list, and ask any dependent questions.

Repeat until no unresolved scope decision prevents assessment. If none exist, say so at the checkpoint.

**Output:** Settled scope decisions and clearly identified unknowns.

## 1.6 — Save the scope assessment

Record the agreed scope, acceptance criteria, exclusions, assumptions, and scope findings.

### Phase 1 is complete when

- The review target and boundaries are explicit.
- Each requirement has a place in the review.
- Missing or extra scope is recorded.
- No unanswered question prevents judging the PR’s purpose.

**Next:** Hold the phase checkpoint described in SKILL.md. Start phase 2 only after the user says to continue.
