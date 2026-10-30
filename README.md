# claude-skills

My personal agent skills.

```bash
npx skills add Davidengstrom01/claude-skills
```

| Skill | What it does |
|-------|--------------|
| [grill-me](skills/grill-me/SKILL.md) | Interview relentlessly about a concept, design, or open decisions until understanding is shared |
| [multiphase-pr-review](skills/multiphase-pr-review/SKILL.md) | Phased PR review: scope, architecture, code quality, validation |
| [multiphase-pr-review-async](skills/multiphase-pr-review-async/SKILL.md) | Unattended phased review left as a pending GitHub review, then a grill on the author's idea and implementation decisions; follow-up review only when re-requested |
| [pr-split](skills/pr-split/SKILL.md) | Split a large branch or PR into smaller draft PRs, stacked or targeting main |
| [publish-pr](skills/publish-pr/SKILL.md) | Publish a draft PR with a ticket-key title and New/Old Behavior description |
| [ship-review](skills/ship-review/SKILL.md) | Post review findings as a GitHub PR review |
| [take-feedback](skills/take-feedback/SKILL.md) | Triage PR review comments with 👍/👎 reactions and drafted replies |
| [thermo-nuclear-code-quality-review](skills/thermo-nuclear-code-quality-review/SKILL.md) | Extremely strict maintainability review |
| [ticketed](skills/ticketed/SKILL.md) | Create Jira tickets in the current sprint with Background + Acceptance criteria |

These are opinionated. `publish-pr` and `ticketed` hardcode my conventions, so fork and edit them to make them yours.
