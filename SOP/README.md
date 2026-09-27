# Standard operating procedures

This folder holds the standard operating procedures (SOPs) that every Bot on the SHP Development team follows. The team overview, roles, work classification, workflow, controls, and approval gates are in the [root README](../README.md). SOPs don't repeat that content. They describe how to carry it out.

Every new product repository starts from this repository, so it carries this folder with it. A product repository may add SOPs of its own under `/docs/SM`, but it never edits these without a change here first.

## Index

| ID | Title | Owner | Status | Approved |
|---|---|---|---|---|
| [SOP-001](SOP-001-token-efficiency.md) | Token efficiency: Bots, Cloud Agents, and model assignment | CTO | Approved | CTO and founder, 2026-09-24; SM-016, 2026-09-26 |
| [SOP-002](SOP-002-bot-profiles-skills-and-routines.md) | Bot profiles, skills, and routines | CTO | Approved | CTO and founder, 2026-09-26 |
| [SOP-003](SOP-003-bot-identities-and-access.md) | Bot identities and access | CTO | Approved | CTO and founder, 2026-09-26 |
| [SOP-004](SOP-004-new-product-repository.md) | New product repository | Cloud Engineer, with the CTO | Approved | CTO and founder, 2026-09-26 |
| [SOP-005](SOP-005-cloud-agent-launch.md) | Cloud Agent launch | Software Architect | Approved | CTO and founder, 2026-09-26 |
| [SOP-006](SOP-006-document-templates.md) | Document templates | Product Manager, with the Software Architect | Approved | CTO and founder, 2026-09-26 |
| [SOP-007](SOP-007-pull-request-review-and-qa.md) | Pull request, review, and QA | Code Reviewer, with the QA Engineer | Approved | CTO and founder, 2026-09-26 |
| [SOP-008](SOP-008-flow-queue-and-reviews.md) | Flow, queue, and reviews | Scrum Master | Approved | CTO and founder, 2026-09-26 |
| [SOP-009](SOP-009-escalation-and-urgent-risks.md) | Escalation and urgent risks | CTO | Approved | CTO and founder, 2026-09-26 |
| [SOP-010](SOP-010-releases.md) | Releases | CTO, with the Cloud Engineer | Approved | CTO and founder, 2026-09-26 |
| [SOP-011](SOP-011-autonomous-execution.md) | Autonomous execution | CTO, with the Scrum Master | Approved | CTO and founder, 2026-09-26 |

Process v2.0 (SM-016, SM-017, SM-018) was approved by the CTO and founder on 2026-09-26 and is effective when SM-018 merges. Approved process v1.1 at [commit `d323687`](https://github.com/sheepnir/GrokBot_DevFlow/tree/d323687c3545eb9097c20b4561a0e9b394a3d969) is superseded.

Bot identities in place 2026-09-24 (#6). The identity model is recorded in [SOP-003](SOP-003-bot-identities-and-access.md), and SOP-001 rules 1 through 6 are in full effect from that date.

## Rollout assets

- [Versioned profiles and shared collaboration rules](profiles/README.md): approved instructions for all ten Bots, not yet verified as installed.
- [Activation, control tests, and pilot](rollout/activation-checklist.md): evidence-based rollout steps for SM-016, SM-017, and SM-018.

## Conventions

- One SOP per file, named `SOP-NNN-short-title.md`, numbered in the order they are proposed.
- Each SOP has the same sections: purpose, scope, rules, escalation, and a change history table.
- Rules are written as things a Bot can check before it acts. "Prefer" and "consider" aren't rules.
- Product and model names change. Anything that names a specific model lives in a single table inside the SOP, so a name change is a one-row edit.
- An SOP is binding only once the index shows it as Approved with a date. Until then it's a draft, and the root README's process change log records the approval as a numbered change.
