# SOP-006: Document templates

The five documents the process runs on, and the IDs that link them.

| | |
|---|---|
| Owner | Product Manager, with the Software Architect |
| Status | See the [SOP index](README.md). |
| Applies to | The PM, the Designer, the Architect, and the Scrum Master, and every role that links to their documents |
| Related | [Root README](../README.md), [SOP-004](SOP-004-new-product-repository.md), the templates in [`SOP/templates`](templates/) |

## Purpose

The root README promises that stories link to product, design, and architecture documents, that the TDD is linked to the BRD's requirement IDs, and that decisions are written down. That only works if the documents have a fixed shape and stable IDs. This SOP fixes both, so a Bot can find a requirement by its ID and a Cloud Agent can be pointed at one section instead of a whole document.

## Scope

Five documents: the Business Requirements Document, the design specification, the Technical Design Document, the decision record, and the sprint record. Each has a template under `SOP/templates`. Other documents in a `/docs` folder are free-form, but they link to these by ID rather than restating them.

## Documents and IDs

| Document | Template | Lives in | ID pattern | Owner |
|---|---|---|---|---|
| Business Requirements Document | [`brd.md`](templates/brd.md) | `/docs/product/brd/<product-or-feature>.md` | Requirements `REQ-<AREA>-NNN`, stories `US-NNN` | PM |
| Design specification | [`design-spec.md`](templates/design-spec.md) | `/docs/UX/specs/<flow>.md` | `DS-NNN` | Designer |
| Technical Design Document | [`tdd.md`](templates/tdd.md) | `/docs/architect/tdd/<feature>.md` | `TDD-NNN`, sections keyed by the `REQ` IDs they cover | Architect |
| Decision record | [`decision-record.md`](templates/decision-record.md) | `/docs/architect/decisions/ADR-NNN-<slug>.md` for technical decisions, `/docs/product/decisions/PD-NNN-<slug>.md` for product ones | `ADR-NNN`, `PD-NNN` | Architect, PM |
| Sprint record | [`sprint-record.md`](templates/sprint-record.md) | `/docs/SM/sprints/SPRINT-NNN.md` | `SPRINT-NNN` | Scrum Master |

## Rules

1. IDs are assigned once and never reused. A requirement that's dropped keeps its ID and gets the status Dropped, with the reason and the decision that dropped it.
2. `<AREA>` in a requirement ID is a short capitalized code for the product area, chosen by the PM and listed at the top of the BRD. `REQ-AUTH-003` is readable in a pull request title. `REQ-003` isn't.
3. Every requirement has testable acceptance criteria written as conditions, not as descriptions of the feature. If QA can't turn a criterion into a check, the PM rewrites it.
4. A BRD separates the first version from later phases. Everything not in the first version is listed under Later, with its ID, so it can be referred to without being built.
5. A TDD covers requirements by ID. A requirement the TDD doesn't cover isn't ready for a sprint. A section of the TDD that covers no requirement is a decision record, not a design.
6. A design spec covers every state of each screen it describes: loading, empty, error, and permission-denied, as well as the happy path, because the Frontend Engineer owns all of them.
7. A decision record is written when a decision would be expensive to reverse, or when it was contested. It records the options considered, not just the choice.
8. A document's status is one of Draft, In review, Accepted, or Superseded, in its header. Acceptance is recorded with a date and the accepting role, per the root README's gates. Silence isn't acceptance.
9. Documents link to each other by ID and path. They don't copy content across folders. A Cloud Agent is pointed at a section, not a document.
10. A change to an Accepted document goes through a pull request to its folder, reviewed by its owner, and bumps the document's version line. Requirements changed after a story is in a sprint are raised with the Scrum Master, because they change the sprint.

## Escalation

- A story whose requirement can't be found by ID goes back to Backlog until the PM fixes the link.
- A conflict between a BRD and a TDD is resolved by the PM and the Architect in two exchanges, then by the CTO, per the root README.

## Change history

| Date | Change | Approved |
|---|---|---|
| 2026-09-24 | First draft, with five templates | Pending CTO and founder |
