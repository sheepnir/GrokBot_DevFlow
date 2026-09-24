# Standard operating procedures

This folder holds the standard operating procedures (SOPs) that every Bot on the SHP Development team follows. The team overview, roles, sprint workflow, and approval gates are in the [root README](../README.md). SOPs don't repeat that content. They describe how to carry it out.

Every new product repository starts from this repository, so it carries this folder with it. A product repository may add SOPs of its own under `/docs/SM`, but it never edits these without a change here first.

## Index

| ID | Title | Owner | Status | Approved |
|---|---|---|---|---|
| [SOP-001](SOP-001-token-efficiency.md) | Token efficiency: Bots, Cloud Agents, and model assignment | CTO | Draft | Pending CTO and founder |

## Conventions

- One SOP per file, named `SOP-NNN-short-title.md`, numbered in the order they are proposed.
- Each SOP has the same sections: purpose, scope, rules, escalation, and a change history table.
- Rules are written as things a Bot can check before it acts. "Prefer" and "consider" aren't rules.
- Product and model names change. Anything that names a specific model lives in a single table inside the SOP, so a name change is a one-row edit.
- An SOP is binding only once the index shows it as Approved with a date. Until then it's a draft, and the root README's process change log records the approval as a numbered change.
