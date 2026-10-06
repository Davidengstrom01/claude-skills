---
name: "publish-pr"
description: "Create and publish a GitHub pull request as a draft, with a ticket-key title and a New/Old Behavior description. Use whenever creating, opening, publishing or pushing a PR, even if the user just says 'make a PR'."
---

# Publish PR

Use this skill for every pull request you create. Reviewers read the PR to understand what changes for the system and its users, so the title and description are about behavior, not about code.

## 1. Find the ticket key

Every PR title starts with a ticket key such as `NOVA-9999`. Look for it in this order:

1. The current branch name (`git branch --show-current`), e.g. `feature/NOVA-1234-fix-login` gives `NOVA-1234`.
2. The commit messages on the branch (`git log <base>..HEAD --format=%s%n%b`).
3. If neither has one, ask the user. Don't invent a key.

A ticket key looks like `[A-Z][A-Z0-9]+-\d+`. Write it in upper case.

## 2. Understand the change

Read the diff against the base branch (`git diff <base>...HEAD`) and the commit messages. Work out:

- What the system does after this change is merged.
- What it does today: what is wrong, missing or not working.

Think about behavior someone would notice: API responses, messages sent or handled, data written, UI, errors, performance. Leave file names, classes and refactoring steps out. That information is already in the diff.

## 3. Write the title

Format: `<TICKET-KEY>: <short summary>`

- Example: `NOVA-9999: Retry failed message deliveries to SignedIn`
- Keep the summary short and focused on the effect. Use imperative mood and no trailing period.

## 4. Write the description

Use exactly this template, with these two sections and nothing else:

```markdown
## New Behavior
<The effect of the change: the new state after the code has been merged.>

## Old Behavior
<The current behavior: what is wrong or not working today.>
```

Guidelines:

- Write a few sentences or short bullets for each section, in plain language a reviewer can follow without opening the code.
- Don't list changed files, classes or methods, and don't explain why each file changed.
- Don't add other sections like Summary, Changes, Testing or Checklist.
- If a change has no behavior change (a pure refactor, for example), say so plainly in New Behavior, e.g. "No functional change; X is now handled by Y, which makes Z easier." In Old Behavior, describe the current state.

**Example**

Title: `NOVA-4821: Skip inactive employees when syncing to Nova`

```markdown
## New Behavior
The nightly sync only sends active employees to Nova. Inactive employees are skipped and logged at info level, so the sync finishes without errors.

## Old Behavior
The sync sent every employee, including inactive ones. Nova rejects inactive employees, so each sync run ended with a batch of failed messages in the error queue.
```

## 5. Create the PR as a draft

Always create the PR as a draft so the author can check it before reviewers are notified:

```bash
git push -u origin HEAD
gh pr create --draft --title "<title>" --body-file <file>
```

Write the body to a temporary file and pass it with `--body-file` so the markdown survives shell quoting. If a PR already exists for the branch, update its title and body with `gh pr edit` instead of creating a new one, and leave its draft/ready state as it is.

When it's done, give the user the PR link and the title in one short line.