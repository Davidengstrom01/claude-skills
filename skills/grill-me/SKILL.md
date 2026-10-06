---
name: "grill-me"
description: "Interview the user relentlessly about a concept, design, or open decisions until you share an understanding. Use on 'grill me', or when another skill (e.g. multiphase-pr-review) calls for a grilling session."
---

# Grill me

Interview me relentlessly about every aspect of this concept until we reach a shared understanding. Walk down each branch of the design tree resolving dependencies between decisions one by one.

If a question can be answered by exploring the codebase, explore the codebase instead.

For each question, provide your recommended answer.

## When called from another skill

The calling skill supplies the concept: the open decisions, assumptions, or findings to grill. Grill only that concept, and follow any stopping rule or recording rule the calling skill defines. When the session ends, hand a short summary of the decisions made back to the calling skill.