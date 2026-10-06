# Phase 4 — Validate and prepare the final review

## 4.1 — Combine the findings

Collect findings from all phases. Merge duplicates that share a root cause. Keep phase labels for traceability.

Keep confirmed issues, unresolved questions, accepted risks, and optional suggestions distinct. Do not add cosmetic findings to fill out the review.

**Output:** One findings list.

## 4.2 — Reassess each finding

Confirm that each finding applies to the reviewed version, falls within scope, has a concrete consequence, and accounts for existing safeguards and decisions.

Establish whether the PR introduces or worsens the problem, or whether it already existed. State when an issue is pre-existing. Inherited problems do not justify requesting changes unless this PR introduces a new defect with real consequences through its interaction with them; show that causal link.

Give every candidate a disposition: confirmed issue, question needing an answer, optional suggestion, accepted risk, dismissed, or unverified. Keep unverified claims separate from confirmed findings.

**Output:** A final disposition for every candidate.

## 4.3 — Resolve disputed findings

Discuss only disputes that could change a finding, its severity, or the requested action. Use the shared grilling session for unresolved decisions. Investigate facts available in the code or documents directly.

Keep unresolved factual claims clearly separate from confirmed findings. If a missing answer prevents a sound assessment, mark the affected work waiting. Do not turn uncertainty into an accusation or hide a verified defect behind a question.

**Output:** Final findings and any remaining review limits.

## 4.4 — Draft the review in the user's voice

Order findings by severity. Give each finding a clear title, code location, trigger, impact, and requested outcome. Keep those details concise; do not force each into a separate heading in the posted comment.

### Comment voice

- **Short.** Use one to three sentences. Go longer only to propose a concrete restructure.
- **Ask, do not assert preferences.** Use questions such as "intentional?", "align?", "or whats your thoughts?", or "...right?" to invite the author's context. State verified facts plainly, then ask about the remedy. Do not hedge a verified fact.
- **No em dashes. Ever.** Use commas, periods, or parentheses in comments and the summary.
- **Casual and lowercase-ish.** Contractions without apostrophes fit this voice: doesnt, thats, havent, isnt. Use "Hmm," to open a real doubt. Words such as abit, mby, and rn are fine where natural; do not force them into every comment.
- **Label optional asks.** Use "not blocking" or "fine as a follow-up". If the user explicitly includes a cosmetic comment, prefix it with `nit:`. This voice rule does not override the blocker-focused review scope or invite a new list of nits.
- **Pair every complaint with a concrete alternative** or a fenced `suggestion` block. Never leave a complaint without a proposed way to resolve it. Use a suggestion block only when the replacement is valid for the anchored lines.
- **Show real uncertainty.** For example: "Dont know the legacy data well enough to say" or "Dunno X well." Never fake uncertainty.
- **Say when something is pre-existing.** It changes the meaning and urgency of the feedback.
- **Be direct about real problems, never dismissive of the person.** "This is spaghetti" can fit a supported structural concern, but explain the concrete problem and follow it immediately with the fix.
- **Use emoji sparingly:** 👍 🤔 😄.
- **Give specific credit in the summary.** Name what the PR got right when the evidence supports it. Omit vague praise.

### Verdict bar

- **APPROVE with comments** is the default for a sufficiently assessed PR with no damaging issue or unanswered merge-critical question. Substantive optional asks do not prevent approval.
- **COMMENT** when something genuinely needs an answer before merge. State the question and why it matters. A COMMENT review does not itself enforce a merge block.
- **REQUEST_CHANGES** only for damaging issues: data loss, security problems, or a new defect with real consequences that this PR introduces. Inherited problems, style preferences, and structural smells alone never reach this bar.

The final verdict follows this bar, even if an earlier phase called a structural concern a blocker. Retain a substantial improvement as an optional ask where useful, or drop it if its benefit does not justify the comment. Do not inflate its severity to fit the earlier label.

Do not issue a confident approval when missing evidence prevents a sound assessment. Keep the review partial or qualified and explain what is missing.

### Summary body

Use the same voice in two or three lines. Include specific credit where warranted, the verdict, and the one or two things worth looking at. Use "rest is nits" only when it is accurate; do not imply there are nits when none were retained or minimize unresolved risks.

**Output:** A complete review ready for the user to read, including every proposed inline comment, the summary body, and the verdict.

## 4.5 — Check that the review is still current

Compare the current PR base and head with the reviewed version. If either changed, reassess affected findings and checks before finalizing the report.

Verify every proposed `path:line` anchor against the current diff and the file with line numbers. Do not count lines by eye. Use `RIGHT` for the updated side and `LEFT` for the original side where appropriate. A file line alone does not prove that GitHub accepts it as a diff anchor.

Put findings without a valid inline anchor in the summary instead of attaching them to unrelated lines.

**Output:** A review tied to a verified version, with valid comment anchors.

### Phase 4 is complete when

- Every candidate has a final disposition.
- Findings are distinct, supported, and actionable.
- The verdict matches the evidence and the verdict bar above.
- The report states what was reviewed and any limits.
- The comments and summary follow the user's voice.
- Proposed inline comments have verified diff anchors.

Publication is a separate status. A completed private review does not require posting.

### Checkpoint before publication

Hold the phase checkpoint described in SKILL.md. Explain the final findings, dispositions, and verdict, and grill any remaining disputes. Move to 4.6 only after the user says to continue.

## 4.6 — Approve the draft, then publish when authorized

### Present the exact draft

Show every comment verbatim with its `path:line` anchor and diff side, followed by the exact summary body and verdict. Include the reviewed version. Do not ask the user to approve an outline in place of the actual comments.

When publication is requested, ask for the go with a "let me edit first" option. Use `AskUserQuestion` if the environment provides it, or an available approval-capable question mechanism or a direct chat question. Do not depend on a tool name that is unavailable, and do not use a question tool that forbids approval requests.

Explain that this phase requires approval of the exact draft before posting. Never post unasked. If the user already approved that exact draft for the same version, do not ask again. Apply requested edits and obtain approval of the revised draft unless that revision was itself explicitly authorized for posting.

### Publish the approved review

Recheck the target PR, version, and anchors immediately before posting. If the version changed, reassess the affected content and present the updated draft for approval. Do not silently publish a stale or substantively different review.

Use a supported GitHub connector or CLI. For CLI publication, write the exact payload to a scratch file, preserving newlines and Markdown, then submit it as JSON. The payload should include:

```json
{
  "commit_id": "VERIFIED_HEAD_SHA",
  "event": "APPROVE",
  "body": "EXACT_APPROVED_SUMMARY",
  "comments": [
    {
      "path": "VERIFIED_REPOSITORY_RELATIVE_PATH",
      "line": 123,
      "side": "RIGHT",
      "body": "EXACT_APPROVED_COMMENT"
    }
  ]
}
```

Replace all example values with verified values and the approved verdict. `APPROVE`, `COMMENT`, and `REQUEST_CHANGES` are the review event values. Omit the comments array or leave it empty when there are no inline comments.

For example, after resolving the correct repository and PR:

```bash
gh api repos/OWNER/REPO/pulls/NUMBER/reviews -X POST --input work/review.json
```

### Verify what landed

Save the returned review ID and URL. Read back that review's state and summary, then fetch all comments attached to that review, with pagination. Compare the actual comment bodies, paths, lines, and sides with the approved payload. Do not rely on a list of all comments by the same author, which may include earlier reviews.

```bash
gh api repos/OWNER/REPO/pulls/NUMBER/reviews/REVIEW_ID
gh api --paginate repos/OWNER/REPO/pulls/NUMBER/reviews/REVIEW_ID/comments
```

If posting returns an uncertain result, inspect the remote review state before retrying to avoid duplicate comments. Report any missing or rejected anchors and any other differences. Do not claim that all comments posted unless readback confirms it.

Close with what posted, what did not, the review link, and any substantial item better handled in a ticket. Do not create tickets without authorization.

**Output:** Verified publication status and a link, or "Review complete; not published."
