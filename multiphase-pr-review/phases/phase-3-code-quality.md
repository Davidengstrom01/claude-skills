# Phase 3 — Review code quality for substantial blockers

## 3.1 — Establish the review boundaries

Use the scope from phase 1 and design decisions from phase 2. Review structural problems introduced or materially worsened by this PR.

Read surrounding code where needed to understand the impact. Do not reopen settled design choices without new evidence.

**Output:** The changed flows and modules to assess.

## 3.2 — Inspect for structural problems

Look for:

- Business rules or feature checks scattered across unrelated paths.
- Duplicated rules that can produce conflicting behavior.
- State models that require fragile combinations of flags or special cases.
- Type contracts, casts, or silent fallbacks that hide invalid states.
- Abstractions that obscure ownership or make behavior hard to trace.
- Update flows that can leave inconsistent state.
- Modules that combine responsibilities so tightly that changes are hard to isolate or test.

File length, branch count, and wrapper count are clues, not automatic findings.

**Output:** Candidate concerns with code references.

## 3.3 — Look for substantial simplification

For each candidate, identify whether a concrete alternative removes unnecessary states, branches, duplicated rules, or layers.

Check that the alternative preserves required behavior and fits the agreed scope. Consider its cost and risks.

Do not raise a finding merely because another implementation looks cleaner.

**Output:** A feasible remedy and a clear account of what it improves.

## 3.4 — Apply the blocker threshold

Keep a finding only when all of these are true:

- **Relevant:** The PR introduces or materially worsens the problem.
- **Substantial:** It creates a concrete failure risk or a serious obstacle to understanding, testing, or safely changing the affected behavior.
- **Supported:** Code paths, contracts, tests, or a specific scenario demonstrate the concern.
- **Actionable:** A proportionate change can address it within this PR.
- **Necessary now:** There is a clear reason to address it before merging.

For a maintenance blocker, name the affected task and explain why the structure makes it unsafe or substantially harder. “This may become difficult later” is insufficient.

Discard naming preferences, small duplication, trivial wrappers, speculative abstractions, and file-size complaints without demonstrated impact. Do not collect them in a separate list of nits.

**Output:** Only findings that justify holding the merge.

## 3.5 — Challenge each remaining finding

Try to disprove it. Check existing safeguards, callers, tests, domain rules, and the decisions from phase 2.

Run focused checks where they would settle the concern. Record any verification limits. Merge findings that share one root cause.

**Output:** Supported blockers, dismissed candidates, and unresolved questions kept separate.

## 3.6 — Prepare targeted grilling where needed

Prepare these questions for the checkpoint grilling session. Use them only when a blocker depends on an unclear constraint or disputed tradeoff:

1. State the concern and evidence.
2. Explain the proposed remedy and its cost.
3. Ask the unresolved decision.
4. Record the answer and reassess the finding.
5. Repeat only if the answer creates another material question.

Do not ask the user to resolve facts available in the code. Do not drop an evidenced defect merely because the user prefers the current implementation.

**Output:** A settled assessment, a documented accepted risk, or a named missing input.

## 3.7 — Save the phase assessment

For each blocker, record:

- The problem and code location.
- The concrete consequence or affected maintenance task.
- Supporting evidence.
- Why it needs attention before merge.
- The smallest sufficient remedy.

If none qualify, state: “No findings meet the blocker threshold.”

### Phase 3 is complete when

- The agreed review areas have been assessed.
- Every candidate has been confirmed, dismissed, or explicitly left unverified.
- Each reported blocker meets the threshold in 3.4.
- Material disputes are settled or clearly identified as limits.
- Verification results and limits are recorded.
- No minor cleanup or preference-based findings remain.

Completing this phase does not require fixing its blockers. An unresolved question that prevents a sound verdict leaves the affected assessment waiting.

**Next:** Hold the phase checkpoint described in SKILL.md. Start phase 4 only after the user says to continue.
