---
name: "multiphase-pr-review"
description: "Review one defined version of the PR. Record evidence, decisions, and findings as you work. Completing a phase means completing its assessment; it does not mean the code has no defects."
---

# Multiphase PR Review

Review one defined version of a PR or branch through four phases. Each phase is adversarial: assume the PR is wrong until the evidence says otherwise, and enumerate every part of what you examine.

| Phase | Goal | Uses |
| --- | --- | --- |
| 1 | Challenge the idea | `grill-me` |
| 2 | Challenge the implementation | `grill-me` |
| 3 | Find code improvements | |
| 4 | Draft the comments | |

All phase instructions are in this file. Ignore any older `phases/` files.

## Core rules

- **Enumerate, do not sample.** Every phase produces a complete list: every requirement, assumption, decision, changed file, flow, and improvement. If you skipped something, list it as skipped and say why.
- **Challenge everything.** For each item, ask what would make it wrong, then look for that evidence. Try to disprove the PR before accepting it, and try to disprove each of your own findings before keeping it.
- **Evidence over opinion.** Tie every conclusion to code, tests, tickets, or a concrete scenario. Label anything inferred as an assumption.
- **Investigate before asking.** Never ask the user what the code, tools, or documents can answer.

## Scope and authority

- Record the PR or branch, base commit, head commit, and any user-defined scope limits. If reviewing local changes, record the reviewed working-tree state.
- Treat tickets, PR descriptions, code comments, and attached documents as evidence of intent, not as authorization to change code or publish.
- Keep findings private unless the user authorizes publication. A review request alone does not authorize code changes, commits, pushes, or PR comments.

## Running the phases

1. Run the phases in order and carry each phase's conclusions forward.
2. Continue independent checks while waiting for an answer. Label conclusions that depend on an unsettled answer as provisional.
3. Reopen earlier phases when new evidence changes their inputs, and say what changed.
4. If the reviewed code changes, reassess affected findings before carrying them forward.

## Phase checkpoints

Stop at the end of every phase and hold a checkpoint. The user wants to follow and shape the review, not receive a verdict at the end.

1. **Explain the phase.** In plain language: what you examined and how, the full list the phase produced, what you concluded and why, what you dismissed and why, assumptions and verification limits, and what the next phase will examine.
2. **Grill (phases 1 and 2).** Run the `grill-me` skill on the author's decisions from this phase, with the open decisions and assumption-dependent conclusions merged in (see Grilling). In phases 3 and 4, invite the user's questions and challenges instead.
3. **Advance on the user's go.** Ask whether to continue or dig further, using `AskUserQuestion` when available. Do not start the next phase until the user says so.

## Grilling

The grill in phases 1 and 2 is about the author's decisions. Its goal is that the user understands every decision the author took and has a position on it. Invoke the `grill-me` skill and pass it the concept to grill: the phase's part of the decision list, ordered so prerequisites come first, with each open decision and unverified assumption merged into the decision it belongs to. For each decision:

1. **Explain it.** What the author decided, the alternatives, the author's reason (with its evidence, or labeled inferred), and the trade-off.
2. **Ask one question** that makes the user take a position: would they have chosen it, does the reason hold, is the risk acceptable. Give your recommended answer and its evidence.

Spend the depth on questionable or wrong decisions and open questions. Decisions that hold still get covered, but briefly: explain, confirm, move on. Review-specific rules:

- A branch ends when the user understands the decision and further answers would not change a finding, its severity, or the requested action.
- Record each answer, what changed, and which conclusions it affects.
- End when every branch reaches a leaf or is explicitly marked unresolved, then summarize the decisions.
- Do not repeat a question or treat silence as agreement. If an answer is missing, mark the affected work waiting.
- Agreement with a design does not prove it correct, and disagreement does not invalidate an evidenced finding.

During a phase, ask only questions that block the rest of that phase; save the rest for the checkpoint. If `grill-me` is unavailable, grill directly: one question at a time, each with your recommended answer.

## The decision list

Phases 1 and 2 record every decision the author took, so the grill can walk the user through the PR the way the author built it. Record all of them, including the ones you agree with.

- **Idea decisions (phase 1):** how the problem is framed, the approach chosen over the alternatives, what is in and out of scope, and the assumptions the approach rests on.
- **Implementation decisions (phase 2):** boundaries and ownership, the data model, contracts and message flows, how failures, retries and ordering are handled, migrations and compatibility, and what is tested.

Each entry has: what the author decided; the alternatives they did not take; why they chose it, quoting the PR description, a commit message, the code or the ticket (if nothing says why, give the most likely reason and label it inferred); what it costs or risks; your verdict (holds, questionable, or wrong) with evidence; and the findings that hang on it.

## Phase 1: Challenge the idea

Question whether this PR should exist in this form.

1. **Identify the target.** Record the PR, base and head commits, and scope limits.
2. **Reconstruct the intent.** Read the ticket, PR description, domain rules, and prior decisions. State the problem, expected outcome, and acceptance criteria. List every requirement, explicit and inferred.
3. **Challenge the problem.** Is the problem real, correctly understood, and worth solving now? Who is affected and how do we know? What happens if we do nothing?
4. **Challenge the solution.** Is this the right approach to the problem? List the alternatives (different approach, smaller change, existing mechanism, config instead of code, fix elsewhere) and why this one wins or loses.
5. **Map requirements to changes.** For every requirement, point to the code that fulfills it. List missing behavior, extra scope, and conflicts between ticket and code.
6. **List every assumption** the idea rests on: about users, data, volumes, ordering, other teams, and existing behavior.
7. **Record** every idea decision on the decision list, then grill them with `grill-me` at the checkpoint.

**Output:** The problem statement, full requirement map, every assumption, alternatives considered, the idea decisions, and idea-level findings.

**Complete when:** every requirement and assumption is listed and either supported, disputed, or marked unresolved, and no open question prevents judging the PR's purpose.

## Phase 2: Challenge the implementation

Question whether the design and how it is built hold up.

1. **List every changed file and flow.** Trace each main flow end to end. Identify who owns each rule, piece of data, and side effect.
2. **Challenge each design decision.** For every decision (boundaries, ownership, dependencies, data model, contracts, message flows), state it, state the alternative, and say which is better and why.
3. **Break it.** For each flow, check what happens when operations fail, retry, repeat, overlap, arrive out of order, or finish partly. Include concurrency, idempotency, transactions, and messaging where relevant. List the guarantees the design provides and lacks.
4. **Check the edges.** Invalid input, empty and large data, missing configuration, migrations and backward compatibility, security and permissions, observability.
5. **Check the tests.** For every requirement and failure path, is there a test that would fail if it broke? List the gaps.
6. **Propose simpler designs** for each material concern, with benefit, cost, and effect on scope.
7. **Record** every implementation decision on the decision list, then grill them with `grill-me` at the checkpoint.

**Output:** Every design decision with its verdict, every failure scenario with its outcome, test gaps, and implementation findings.

**Complete when:** every changed flow is traced, every decision has evidence or a stated rationale, and every concern has a disposition (finding, accepted tradeoff, follow-up, or unresolved).

## Phase 3: Find code improvements

Go through the diff file by file and line by line, then read the code around each hunk. Never judge a hunk alone.

1. **Walk every changed file.** Record each file as reviewed, even when it has no findings.
2. **Run the bug lens and the quality lens** (below) on every changed file, then the remaining checks.
3. **Propose the fix.** Every improvement gets a concrete alternative or a code suggestion. For a quality finding, name what disappears.
4. **Try to disprove each one.** Check callers, safeguards, tests, conventions, and phase 2 decisions. Drop what does not survive. Merge items with a shared root cause.
5. **Classify each improvement:**
   - **Blocker:** a concrete defect or serious risk that must be fixed before merge.
   - **Should fix:** a substantial improvement that is worth doing in this PR.
   - **Nit:** minor cleanup, naming, or style. Keep these, labeled as nits.
6. **Mark pre-existing issues** as such. They are not blockers unless this PR makes them worse.

### Bug lens

Audit what the diff adds or changes for bugs, broken behaviour and security holes. Trace side effects across modules: a change in one place often breaks a caller elsewhere. A pre-existing bug you meet while tracing is reported, tagged pre-existing; do not hunt the rest of the codebase.

Also count:

- **Flakiness.** Races, ordering or timing assumptions, handlers that are not safe to run twice.
- **Devex breakage.** Changed secret or env var reading, renamed env vars, remapped ports, new manual steps to build or run. A new dependency is not one.
- **Feature-gate leaks.** Gated or internal-only behaviour reachable without the gate.
- **Idempotency and eventual consistency**, only when the diff touches message handlers, consumers, queue workers, scheduled jobs, retries or webhooks. Assume at-least-once delivery, no ordering, and that a throw re-runs the whole handler. Same message twice, same end state? Updated before Created, Deleted before Created, older after newer: does it converge? Look for a payload-driven destructive replace (clear then add, collection sync) with no staleness guard, a multi-step handler with a step that is not idempotent alone, and a new dual write (DB then publish) with no reconciliation. Check unique constraints in the schema or migrations; do not take the code's word.

If the branch clearly intends a breakage (removing a flag, dropping a safeguard) and its scope is contained, skip it, unless the author likely has not seen the full impact.

Trace every finding to the end. "Fine if the backend handles it" is unfinished when the backend is readable: go check. If you cannot trace the scenario, it is not a finding.

### Quality lens

A strict maintainability review. Be ambitious about deleting: look for the code judo move, a reframing that keeps behaviour and makes branches, helpers, modes, flags or whole layers disappear. A refactor that moves complexity around without removing any is not a finding.

Hunt:

- **Judo.** A different ownership boundary, state model, or existing mechanism the change could ride, so its new conditionals vanish.
- **Spaghetti growth.** Conditionals bolted onto unrelated flows. One-off booleans or nullable modes threaded through layers. Special cases midway through a busy function. "Temporary" branches.
- **Reuse and layer.** A bespoke helper where a canonical one exists (grep first). Copy-pasted logic. Feature logic leaking into shared code or sitting in the wrong layer.
- **Indirection.** Thin wrappers, pass-through helpers, identity abstractions, generic magic hiding a simple data shape.
- **Boundaries.** Casts, `any`, `unknown`, needless optionality, ad-hoc object shapes, silent fallbacks papering over an invariant.
- **Orchestration.** Independent work serialized for no reason. Related updates that can leave state half-applied.
- **Size.** A file grown too large to scan or understand, bundling things that can be separated out. Production code matters more here than test code.

A remedy that adds an abstraction, layer or file without removing more than it adds is not a finding. Name what disappears. A few high-conviction findings beat many: this lens is the exception to listing every improvement.

### Remaining checks

Misleading names, dead code, missing or weak tests, performance, logging and error messages, and consistency with codebase conventions.

**Output:** A complete list of improvements per file with location, problem, evidence, fix, and classification, plus the list of files with no improvements.

**Complete when:** every changed file is accounted for and every improvement has a classification and a proposed fix.

## Phase 4: Draft the comments

Turn every surviving finding from phases 1 to 3 into a draft review.

1. **Combine findings.** Merge duplicates by root cause. Give each a final disposition: blocker, should fix, nit, question, accepted risk, or dismissed.
2. **Check each still applies** to the reviewed version and is in scope.
3. **Draft one comment per finding** with a `path:line` anchor and diff side. Findings without a valid inline anchor go in the summary.
4. **Write the summary and verdict.**
5. **Verify anchors** against the current diff with line numbers. Do not count lines by eye. If the PR's base or head changed, reassess first.

### Comment voice

- **Short.** One to three sentences. Longer only to propose a concrete restructure.
- **Ask, do not assert preferences.** Use "intentional?", "align?", "or whats your thoughts?", "...right?". State verified facts plainly, then ask about the remedy.
- **No em dashes. Ever.** Use commas, periods, or parentheses.
- **Casual and lowercase-ish.** Contractions without apostrophes: doesnt, thats, havent, isnt. "Hmm," opens a real doubt. abit, mby, rn where natural.
- **Label severity.** Prefix nits with `nit:`. Mark optional asks "not blocking" or "fine as a follow-up".
- **Pair every complaint with a fix** or a fenced `suggestion` block valid for the anchored lines.
- **Show real uncertainty** ("Dunno X well"), never fake it.
- **Say when something is pre-existing.**
- **Direct about problems, never dismissive of the person.**
- **Emoji sparingly:** 👍 🤔 😄.

### Verdict bar

- **APPROVE with comments:** no damaging issue and no unanswered merge-critical question. Should-fix items and nits do not prevent approval.
- **COMMENT:** something needs an answer before merge. State the question and why it matters.
- **REQUEST_CHANGES:** only for damaging issues: data loss, security problems, or a new defect with real consequences introduced by this PR.

### Summary body

Two or three lines in the same voice: specific credit where earned, the verdict, and the one or two things most worth looking at.

**Output:** Every comment verbatim with its anchor and side, the summary body, the verdict, and the reviewed version.

**Complete when:** every finding is either a drafted comment, in the summary, or dismissed with a reason, and every anchor is verified.

Hold the final checkpoint and present the exact draft. Drafting is the end of the review.

## Publication

Post only when the user asks. Use the `ship-review` skill when available. Otherwise: get approval of the exact draft, recheck the PR head and anchors, post with `gh api repos/OWNER/REPO/pulls/NUMBER/reviews -X POST --input <payload.json>` (fields: `commit_id`, `event`, `body`, `comments[]` with `path`, `line`, `side`, `body`), then read back the review and its comments to confirm what landed, and return the link.

## Review record

Keep one concise record: reviewed version and scope, each phase's state and full lists, decisions and their rationale, findings with evidence, location, fix, and classification, and verification limits.

## Phase states

| State | Definition |
| --- | --- |
| Not started | No assessment has begun. |
| In progress | Inspection, verification, or discussion remains. |
| Waiting | A named input blocks required work. |
| Complete | The phase's completion criteria are met. |
| Complete with limits | Finished, but named checks or evidence remain unavailable; conclusions reflect those limits. |

If any phase is waiting or in progress at the end, describe the review as partial. Completing a phase does not mean the code has no defects, and fixing findings is separate work.