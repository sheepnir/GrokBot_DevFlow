# SOP-004: New product repository

The checklist for starting a product repository from this one.

| | |
|---|---|
| Owner | Cloud Engineer, with the CTO |
| Status | See the [SOP index](README.md). |
| Applies to | The CTO, the Cloud Engineer, and every role that owns a `/docs` folder |
| Related | [Root README](../README.md), [SOP-001](SOP-001-token-efficiency.md), [SOP-003](SOP-003-bot-identities-and-access.md), [Cloud Agents](https://cursor.com/docs/cloud-agent), [Bugbot](https://cursor.com/docs/bugbot), [Rules](https://cursor.com/docs/rules) |

## Purpose

Every new project starts from this repository. A product repository that's set up the same way every time lets the Bots find what they expect, keeps Cloud Agents from spending their runs on setup, and makes the approval gates enforceable from day one. This SOP is the checklist.

## Scope

This SOP covers creating the repository, its settings, its folders, its agent configuration, its automation, and the first GitHub Project. It stops where the first sprint starts, which is SOP-008.

## Before starting

The CTO confirms three things on the product's discovery issue in this repository: the PM has a problem statement, the founder has named the product, and the repository will be public. Repositories are treated as public whether or not they are, so nothing below changes for a private one.

## Checklist

Each item names its owner and what "done" means. The Cloud Engineer runs the checklist and records it in the new repository's `/docs/SM/setup.md`, with the date each item was done.

### Repository and settings

| # | Item | Owner | Done when |
|---|---|---|---|
| 1 | Create the repository from this one as a template, named as the founder chose | Founder | The repository exists with this repository's `SOP`, `.github`, and `assets` folders in it |
| 2 | Add a ruleset on `main`: pull request required, one approval, stale approvals dismissed on push, force pushes blocked, deletions blocked | Founder | The ruleset is active and listed in `/docs/SM/setup.md` |
| 3 | Add the Bot identities that exist to the ruleset's pull request allowance, per SOP-003 | Founder | Every role that authors pull requests can open one |
| 4 | Add `CODEOWNERS` mapping each `/docs` folder to its owning role's identity, and `/SOP` to the CTO | CTO | A pull request to a folder requests its owner's review automatically |
| 5 | Connect the repository to Cursor for Cloud Agents and Bugbot, with Autofix off per SOP-001 rule 20 | Founder | A Cloud Agent can clone and push a branch |

### Folders and documents

| # | Item | Owner | Done when |
|---|---|---|---|
| 6 | Create `/docs/product`, `/docs/UX`, `/docs/architect`, `/docs/SM`, each with a `README.md` that says what the folder holds and who owns it | Each owning role | The four folders exist with the layout from the root README |
| 7 | Copy the document templates from `SOP/templates` into place: the BRD skeleton under `/docs/product`, the TDD and decision record skeletons under `/docs/architect`, the design spec skeleton under `/docs/UX`, and the sprint record skeleton under `/docs/SM` | Each owning role | Each folder has its template, unfilled |
| 8 | Create `/docs/SM/access.md` (the access register from SOP-003), `/docs/SM/setup.md`, and `/docs/SM/skills/` | Scrum Master | The files exist |
| 9 | Write the product `README.md`: what the product is, links to the four folders, how to run it locally once that exists | PM | The README links to every `/docs` folder |

### Agent configuration

| # | Item | Owner | Done when |
|---|---|---|---|
| 10 | Add `AGENTS.md` at the root: how to build, test, and run, where the docs are, and the conventions an agent must follow | Architect | A Cloud Agent given only the repository can run the tests |
| 11 | Add rules under `.cursor/rules/*.mdc`: one for the repository's conventions, one per language or framework in use | Architect | Rules are scoped with globs, not applied to everything |
| 12 | Add `.cursor/environment.json` with the install and start commands, or a Dockerfile, so a Cloud Agent starts with dependencies ready | Cloud Engineer | A Cloud Agent's run begins with a working environment and no install step |
| 13 | Add `.cursor/BUGBOT.md` with the review guidance the Code Reviewer wants Bugbot to apply | Code Reviewer | Bugbot's first review cites it |
| 14 | Add a pull request template. The one in `.github` from this repository is the default and already has the `Model` section. | CTO | Every new pull request opens with the template |

### Automation and environments

| # | Item | Owner | Done when |
|---|---|---|---|
| 15 | Add CI in GitHub Actions: lint, tests, and any build, required to pass before merge | Cloud Engineer | The ruleset requires the CI check |
| 16 | Create separate environments, at least `test` and `production`, with their own credentials and their own cloud roles | Cloud Engineer | Nothing in `test` can reach `production` |
| 17 | Put secrets in the secrets manager and, for Cloud Agents, in Cursor's secret store. Nothing in the repository. | Cloud Engineer | A search of the repository for tokens and keys finds none |
| 18 | Add monitoring and a cost alert for the cloud accounts the product uses | Cloud Engineer | An alert reaches the CTO and the founder |
| 19 | Write the deployment and rollback procedure in `/docs/architect/operations.md` | Cloud Engineer | A release can be rolled back by following it |

### Tracking

| # | Item | Owner | Done when |
|---|---|---|---|
| 20 | Create labels: `owner:<role>` for each role, `points:1` through `points:8`, `blocked`, `access`, `incident`, `security` | Scrum Master | The labels exist |
| 21 | Add issue templates for a story and a defect, with the fields SOP-007 and SOP-008 need | Scrum Master | A new issue opens with the fields |
| 22 | Create the first GitHub Project with the states from the root README's sprint workflow and the fields from SOP-008 | Scrum Master | The Project exists and is empty |

## Rules

1. A product repository isn't used for a sprint until every checklist item is done and recorded. Partial setup is where agents burn their runs.
2. The `SOP` folder in a product repository is a copy. Changes to an SOP happen in this repository and are copied forward. A product repository may add SOPs of its own under `/docs/SM`, numbered from `SOP-101`.
3. Nothing in the checklist creates an external account or credential without the founder doing it, per SOP-003.
4. The checklist is re-run, and `/docs/SM/setup.md` updated, whenever a new environment, cloud account, or integration is added.

## Escalation

- An item that can't be completed as written is raised to the CTO with the reason and a proposed substitute. The substitute is recorded in `/docs/SM/setup.md`.
- A request to skip an item is a process change and goes through the approval gate.

## Change history

| Date | Change | Approved |
|---|---|---|
| 2026-09-24 | First draft | Pending CTO and founder |
