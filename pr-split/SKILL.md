---
name: "pr-split"
description: "Split a large branch or PR into smaller draft PRs, either stacked (each on the previous) or all targeting main. Use on 'pr split', 'split this PR', 'break up this branch', '--stacked', '--main'."
---

# PR Split

Turn one large branch into several small, reviewable draft PRs. Small PRs get faster, better reviews, but only if each one makes sense on its own and nothing from the original change is lost.

## Modes

- `--stacked`: PR 1 targets the base branch, PR 2 targets PR 1's branch, and so on. Use when later parts build on earlier ones.
- `--main`: every PR targets the base branch (usually `main`) and can be merged in any order. Use when the parts are independent.

Accept natural phrasing too ("stacked", "both towards main", "all against main"). If no mode is given, ask the user which one before doing anything else. Don't pick one for them.

## 1. Check the starting point

- Run `git status`. If the working tree is dirty, stop and ask the user to commit or stash. Splitting rewrites the checkout repeatedly and uncommitted work can be lost.
- Note the original branch: `git branch --show-current`.
- Find the base: an existing PR's base (`gh pr view --json baseRefName`), otherwise `origin/HEAD` (usually `main`). Run `git fetch origin` first.
- Find the ticket key the same way as the publish-pr skill (branch name, then commits, then ask).

## 2. Understand the change

Read `git diff origin/<base>...HEAD` and `git log origin/<base>..HEAD`. Group the changes by logical concern, for example: contracts/messages, infrastructure/config, domain logic, consumers/handlers, tests, pure refactors or renames.

Good parts:

- Compile and pass tests on their own (in stacked mode, on top of the previous parts).
- Have one clear purpose a reviewer can describe in a sentence.
- Keep tests together with the code they test.
- Put refactors and renames first, separate from behavior changes.

In `--main` mode every part must be independent of the others. If a part needs another part's code (a new interface, a message contract, a migration), say so and suggest either moving that shared piece into every part that needs it, or switching to `--stacked`.

## 3. Propose the plan and wait

Show the plan and wait for the user to approve or adjust it. Don't create branches before they confirm.

```
Mode: stacked | main    Base: main    Ticket: NOVA-1234

1/3  NOVA-1234-part-1  Add retry contract and config        (base: main)
     src/Contracts/RetryMessage.cs, appsettings.json
2/3  NOVA-1234-part-2  Retry failed deliveries in consumer   (base: part-1 | main)
     src/Consumers/DeliveryConsumer.cs, tests/...
3/3  ...
```

If a file has to be split by hunk across parts, say which hunks go where.

## 4. Build the branches

Name branches `<original-branch>-part-<n>`. For each part, in order:

```bash
git checkout -b <part-branch> <start>
```

`<start>` is `origin/<base>` in `--main` mode, and the previous part's branch in `--stacked` mode (`origin/<base>` for part 1).

Bring the part's changes over from the original branch:

- Whole files: `git checkout <original-branch> -- <paths>` (for deleted files use `git rm`).
- Partial files: `git diff origin/<base>...<original-branch> -- <path> > part.patch`, trim it to the wanted hunks, then `git apply part.patch`.

Commit with a message starting with the ticket key. Build and run tests for the part if the project supports it (e.g. `dotnet build`, `dotnet test`). If a part doesn't build, fix the split (move the missing piece in) rather than shipping a broken PR.

## 5. Verify nothing is lost

- `--stacked`: `git diff <last-part-branch> <original-branch>` must be empty.
- `--main`: create a throwaway branch from `origin/<base>`, merge every part into it, then `git diff <throwaway> <original-branch>` must be empty. Delete the throwaway branch afterwards.

If the diff isn't empty, show what's missing and fix the parts before pushing.

## 6. Push and open draft PRs

Follow the publish-pr skill for every part (draft, ticket-key title, New/Old Behavior body written about that part's behavior only). Add the part number to the title:

`NOVA-1234: (1/3) Add retry contract and config`

```bash
git push -u origin <part-branch>
gh pr create --draft --base <base-or-previous-part> --head <part-branch> --title "<title>" --body-file <file>
```

After all PRs exist, post one comment on each linking the whole set in order, and in `--stacked` mode note that they should be merged from 1 upward:

```bash
gh pr comment <pr> --body "Split of <original-branch>: 1/3 #101, 2/3 #102, 3/3 #103"
```

## 7. Finish

- Leave the original branch and any original PR untouched. Don't force-push or close it; mention the user can close it once the split PRs are reviewed.
- Check out the original branch again.
- Reply with one line per PR: number, title, link.