---
name: "take-feedback"
description: "Fetches GitHub pull request review comments via the gh CLI, judges each one purely on whether it identifies a real correctness/functionality issue, and reacts with a 👍 (agree) or 👎 (disagree) emoji reaction. 👎 verdicts also get a short drafted reply, shown to the user for approval before anything is posted. Use this whenever the user says \"take-feedback,\" asks to \"review PR comments,\" wants to \"react to PR feedback,\" or otherwise wants Claude to triage and respond to reviewer feedback on a pull request — even if they just paste a PR link and ask what to do with the comments on it."
---

# Take Feedback

Reviews the comments left on a GitHub pull request, investigates each one, and proposes a verdict — then waits for the user to confirm or override before reacting with 👍 or 👎. Every 👎 also gets a drafted reply explaining why, which the user sees and approves before it is posted. Judgment is based purely on whether a comment is functionally correct — not style, not phrasing, not preference.

## Dialogue language & attention rules

Apply to every message to the user, and to drafted replies:

- Minimize text: compress, drop fillers and unnecessary words. Stay as concise as possible.
- Options over prose. Use `AskUserQuestion` for anything with a finite answer set; free text only for genuinely open questions.
- A round earns its place: ask only when the answer changes a verdict or a reply.
- No preamble, no retelling of the diff, no closer.

The user's attention is the scarce resource here; every extra word or round costs them more than it gives.

## Prerequisites

1. Confirm `gh` is authenticated: `gh auth status`. If not, tell the user to run `gh auth login` and stop.
2. Identify the target PR. If `owner/repo` + PR number (or a PR URL) is missing, ask before anything else.

## Workflow

### 1. Fetch review comments

```
gh api repos/{owner}/{repo}/pulls/{pr_number}/comments --paginate
```

Inline review comments. Each has `id`, `path`, `diff_hunk`, `body`, `user.login`, and `in_reply_to_id` if it's a thread reply.

### 2. Skip comments already handled

```
gh api repos/{owner}/{repo}/pulls/comments/{comment_id}/reactions
```

If the authenticated user already left `+1` or `-1`, skip it.

### 3. Investigate every remaining comment

Read `body` and `diff_hunk`; for replies, also read the parent comment.

For each, work out:

- **Investigation** — what the comment claims and whether it holds against the code.
- **Proposed verdict** — 👍 agree (real functional issue: bug, edge case, logic error, incorrect behavior) or 👎 disagree (mistaken, misreads the code, no meaningful behavior change, or style/naming/preference with no functional claim).
- **Reasoning** — one line.
- **Draft reply (👎 only)** — a short reply to the reviewer stating why the comment doesn't hold, grounded in the code. Follow the language rules above: one or two sentences, factual, polite, no filler, no "Thanks for the comment". Point at the concrete reason (e.g. "`x` is null-checked on line 42, so this path can't throw.").

Post nothing yet.

### 4. Present everything for confirmation

One message, all comments, numbered table: #, author, short quote, verdict, one-line reasoning. Below the table, list every 👎 draft reply verbatim under its comment number, so the user sees the exact text that would be posted.

Then confirm with `AskUserQuestion`, e.g.:

- Post all as proposed
- Edit replies / override verdicts (user specifies which)
- Cancel

If the user overrides a 👍 to 👎, draft a reply for it and show it before posting. If they edit a reply, use their text exactly. Do not proceed until confirmed.

### 5. Post the confirmed verdicts

Reactions:

```
gh api -X POST repos/{owner}/{repo}/pulls/comments/{comment_id}/reactions -f content='+1'
gh api -X POST repos/{owner}/{repo}/pulls/comments/{comment_id}/reactions -f content='-1'
```

Approved replies for 👎 (as a reply in the comment's thread):

```
gh api -X POST repos/{owner}/{repo}/pulls/{pr_number}/comments/{comment_id}/replies -f body='<approved reply>'
```

👍 gets a reaction only, no reply. Never post a reply the user hasn't seen in its final form.

### 6. Dry-run mode

If the user asks to "preview," "dry run," "test," or "just show me," stop after presenting the table and drafts in step 4. No confirmation question, nothing posted.

### 7. Report back

One short line: reviewed / skipped / 👍 / 👎 / replies posted counts. No recap of the table.

## Notes

- Covers **review comments** (on the diff) only. If the user wants general PR comments too, also pull `gh api repos/{owner}/{repo}/issues/{pr_number}/comments`.
- Posting needs write access; read-only tokens fail at step 5.
- Never go from investigation straight to posting — step 4 confirmation is required every time, except dry-run where nothing is posted.