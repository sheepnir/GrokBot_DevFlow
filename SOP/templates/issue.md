---
name: Work item
about: A story, a fix, or a quality task, classified before build starts
---

<!-- Keep this short. Link to documents and evidence instead of copying them. See the root README, "Classify the work first". -->

## Problem

What's wrong or missing, for whom, and how we'll know it's fixed.

## Acceptance criteria

Conditions QA could check.

- 

## Classification

```markdown
Tier: Small | Standard | Large
Risk flags: none | security, privacy, operations, external
Why: one to three sentences on size, complexity, uncertainty, dependencies, and risk
Classified by: <role>, <date>
```

## Links

<!-- Only what the tier needs. Small: usually none. Standard: requirement IDs, and a design spec or TDD section if one applies. Large: BRD, TDD, design spec, milestone. -->

- Requirements:
- Design:
- Technical design:
- Depends on:

## Feature flag

<!-- The flag that keeps this off in production until it's done, or "none" if every pull request will be complete when it merges. -->

<!-- The owner's claim, progress notes, and handoff notes go in comments below, per SOP-011. The closing comment links the pull requests and the QA verdict. -->
