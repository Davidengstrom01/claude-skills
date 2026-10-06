---
name: "ticketed"
description: "Create Jira tickets assigned to David in the current sprint with a Background + Acceptance criteria description. Use on 'ticketed', 'create a ticket', 'make a Jira issue'."
---

# ticketed

Create Jira tickets through the Atlassian Rovo tools. Every ticket is assigned to the current user and placed in the active sprint.

## Rules

- Project: NOVA by default. Use another project only if the user names one.
- Assignee: always the current user (`atlassianUserInfo` → account id).
- Sprint: always the active sprint of the target project's board.
- Description: always the exact format below. No other sections.
- Issue type: Task unless the user says Bug, Story or similar.
- Language: write the ticket in the language the user uses.

## Core principle: what, never how

A ticket describes the current state and the desired state. It never explains how to solve it; that is for the developer to work out.

Never include:
- implementation steps, technical approaches or suggested solutions
- code, class, method, file or library names as instructions
- phrases like "add a", "refactor", "use X to", "implement by"

If the user's input contains solution details, leave them out of the ticket and mention in one line that they were left out.

## Description format

```
## Background
<Current state: how things are today, plus a short reason why it needs to change. 1-4 sentences.>

## Acceptance criteria
- [ ] <Desired state: an observable, testable outcome>
- [ ] <...>
```

- Background: current state + short reason for the change. No solution.
- Acceptance criteria: the desired state as verifiable outcomes. Describe what is true when done, never how to get there.

Example:
- Good: "- [ ] A user who is removed from a team no longer receives that team's notifications."
- Bad: "- [ ] Add a check in NotificationHandler that filters removed members."

## Steps

1. Get `cloudId` with `getAccessibleAtlassianResources` and the user's account id with `atlassianUserInfo`.
2. Use project key NOVA unless the user named another project.
3. Find the active sprint:
   - `searchJiraIssuesUsingJql` with `project = <KEY> AND sprint in openSprints()`, maxResults 1, requesting the sprint field (usually `customfield_10020`).
   - Read the sprint id from the result. If there are several open sprints, pick the one with state `active`.
   - If no active sprint is found, tell the user and ask whether to create the ticket without a sprint.
4. Draft the summary and description from what the user provided. If the current state, reason or desired state is missing, ask for it. Do not invent it.
5. Check the draft against the core principle and remove anything that describes how.
6. Show the draft (type, project, summary, description) and ask for confirmation.
7. Create the ticket with `createJiraIssue`: set assignee to the account id and the sprint field to the sprint id (a number). If the sprint cannot be set at creation, set it with `editJiraIssue` right after.
8. Reply with the ticket key and link in one line.

## Multiple tickets

If the user describes several tickets, draft them all, confirm once, then create each.