---
name: ship-review
description: "Turn code review findings that already exist in context into a posted GitHub PR review written in the user's voice. Use when findings from a review skill are in context and the user says \"post this as a review\", \"comment these on the PR\", \"put this on github\"."
---

# Ship Review

Takes findings that already exist and turns them into a posted review. Does **not** produce findings.

If there are no review findings in context, stop and say so. Point at the review skills that produce them. Do not audit the diff yourself to fill the gap.

## 1. Re-verify before presenting anything

Findings from a review pass are candidates, not conclusions. Before grilling, check each one:

- **Does the repo actually support the complaint?** A generic rule can be a non-issue locally. Check neighbours before flagging file size, naming, layering, test count, DI style. If the norm contradicts the finding, drop it and say plainly that you are dropping it and why.
- **Did the PR introduce it, or inherit it?** Read the pre-image. This is the single biggest severity driver.
- **Trace the runtime claim to its end.** If you claim a 500, find the handler. If you claim broken access, find the check. Unverified consequences do not get posted.
- **Look once more for what the review missed**, especially anything on the theme the user cares about. New findings are welcome here; unverified ones are not.

Report corrections to your own earlier findings flatly, no hedging or apology.

## 2. Grill for severity and inclusion

The user decides what ships. You supply context and a recommendation.

- Use `AskUserQuestion`. Batch 2-3 findings per round, grouped by what couples them (same subsystem, same root cause). Do not fire one question per finding.
- Recommended option first, labelled `(Recommended)`.
- Always offer "Drop it" as an option, with the honest case for dropping.
- Give the context they may not have: what the mechanism is, what the blast radius is, whether it is pre-existing. Two or three sentences.
- Offer severity *shapes*, not just yes/no: ask with a concrete fix / flag and ask if intentional / note without any ask / drop.
- Nits get one batched question at the end, multi-select.
- Answer their questions straight. If they ask whether something buys anything and it does not, say it does not.

## 3. Draft in the user's voice

- **Short.** One to three sentences. Longer only to propose a concrete restructure.
- **Ask, do not assert.** "Intentional?" "Align?" "Or whats your thoughts?" "...right?" A question invites the author's context; a statement invites defending.
- **No emdashes. Ever.** Commas, periods, or parens.
- **Casual and lowercase-ish.** Contractions without apostrophes read natural here: doesnt, thats, havent, isnt. "Hmm," to open a doubt. abit, mby, rn.
- **`nit:` prefix** for cosmetics. "not blocking" / "fine as a follow-up" for optional asks.
- **Pair every complaint with a concrete alternative** or a ```suggestion block. Never leave a complaint with no exit.
- **Show real uncertainty where it exists.** "Dont know the legacy data well enough to say." "Dunno X well." Never fake it, and never hedge a verified fact.
- **Say when something is pre-existing.** It changes how the author hears it.
- Direct about real problems ("This is spaghetti.") but never dismissive of the person. The fix follows immediately.
- Sparing emoji: 👍 🤔 😄
- Credit what the PR got right, in the summary body, specifically. Vague praise reads as filler.

## 4. Verdict bar

- **APPROVE with comments** is the default, including for substantive optional asks the author can take or leave.
- **COMMENT** when something genuinely needs an answer before merge.
- **REQUEST_CHANGES** only for damaging issues: data loss, security, a new defect with real consequences that this PR introduces. Inherited warts never reach this bar.

Summary body follows the same voice: two or three lines. Specific credit, the verdict, the one or two things worth looking at, "rest is nits."

## 5. Approve, then post

Present every comment verbatim with its `path:line` anchor, plus the summary body and verdict. Then ask for the go via `AskUserQuestion` with a "let me edit first" option. Never post unasked.

Anchor lines must exist in the diff or GitHub silently drops the comment. Read the file with line numbers to pin anchors; do not count by eye.

```bash
gh pr list --head "$(git branch --show-current)" --state all --json number,url
# write review.json to the scratchpad: {event, body, comments:[{path, line, side:"RIGHT", body}]}
gh api /repos/OWNER/REPO/pulls/N/reviews -X POST --input review.json -q '{state, url: .html_url}'
```

Then read back what actually landed, and report any anchor that went missing:

```bash
gh api "/repos/OWNER/REPO/pulls/N/comments?per_page=100" -q '.[] | select(.user.login=="LOGIN") | "\(.path | split("/") | last):\(.line)"'
```

Close by telling the user what shipped and what got dropped, and flag anything worth a ticket rather than a PR fix.