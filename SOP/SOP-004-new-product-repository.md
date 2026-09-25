# SOP-004: New product repository

The checklist for starting a product repository from this one.

| | |
|---|---|
| Owner | Cloud Engineer, with the CTO |
| Status | See the [SOP index](README.md). |
| Applies to | The CTO, the Cloud Engineer, and every role that owns a `/docs` folder |
| Related | [Root README](../README.md), [SOP-001](SOP-001-token-efficiency.md), [SOP-003](SOP-003-bot-identities-and-access.md), [Cloud Agents](https://cursor.com/docs/cloud-agent), [Bugbot](https://cursor.com/docs/bugbot), [Rules](https://cursor.com/docs/rules) |

## Purpose

Every new project starts from this repository. A product repository that's set up the same way every time lets the Bots find what they expect, keeps Cloud Agents from spending their runs on setup, and makes the controls in the root README enforceable from day one. This SOP is the checklist.

## Scope

This SOP covers creating the repository, its settings and controls, its folders, its agent configuration, its automation, and its GitHub Project. It stops where the first issue is claimed, which is SOP-011.

## Before starting

The CTO confirms three things on the product's discovery issue in this repository: the PM has a problem statement, the founder has named the product, and the repository will be public. Repositories are treated as public whether or not they are, so nothing below changes for a private one.

## Checklist

Each item names its owner and what "done" means. The Cloud Engineer runs the checklist and records it in the new repository's `/docs/SM/setup.md`, with the date each item was done. Items marked C1 to C9 set up the controls in the root README. Until an item is done, `setup.md` shows its control as not enforced. C5 and C6 are written controls by design, and `setup.md` says so. Roles without a GitHub identity make their changes through Cloud Agents and the CTO, per SOP-003.

### Repository and settings

| # | Item | Owner | Done when |
|---|---|---|---|
| 1 | Create the repository from this one as a template, named as the founder chose | Founder | The repository exists with this repository's `SOP`, `.github`, and `assets` folders in it |
| 2 | Add a ruleset on `main` (C1 to C4). Target: the default branch. Bypass list: empty. Restrict deletions: on. Block force pushes: on. Require a pull request before merging: on, with required approvals set to 1, "Dismiss stale pull request approvals when new commits are pushed" on, "Require approval of the most recent reviewable push" on, "Require conversation resolution before merging" on, and "Require review from Code Owners" off, because a required code owner who opens a pull request would block it. Require status checks to pass: on, with "Require branches to be up to date before merging" on and the `ci` check from item 16 required. | Founder | The ruleset is active, and each setting is listed in `/docs/SM/setup.md` |
| 3 | Limit pull request creation to the founder and `shpdev-cto`, and add `shpdev-reviewer` as a collaborator with Write so its approvals count, per SOP-003 | Founder | The CTO can open a pull request, and a `shpdev-reviewer` approval satisfies the ruleset |
| 4 | Add `CODEOWNERS` with `* @shpdev-reviewer`, so every pull request requests the Code Reviewer automatically. Record the sensitive paths in `setup.md` for C6, a written control: authentication and authorization code, migrations, infrastructure, `.github/workflows/`, and the production feature flag settings. | CTO | A new pull request requests `shpdev-reviewer` by itself, and the Code Reviewer checks the path list for missing risk flags |
| 5 | Turn on secret scanning and push protection (C9) | Founder | A test push with a fake token pattern is blocked |
| 6 | Connect the repository to Cursor for Cloud Agents and Bugbot, with Autofix off per SOP-001 rule 20 | Founder | A Cloud Agent can clone and push a branch |

### Folders and documents

| # | Item | Owner | Done when |
|---|---|---|---|
| 7 | Create `/docs/product`, `/docs/UX`, `/docs/architect`, `/docs/SM`, each with a `README.md` that says what the folder holds and who owns it | Each owning role | The four folders exist with the layout from the root README |
| 8 | Copy the document templates from `SOP/templates` into place: the BRD under `/docs/product`, the TDD and decision record under `/docs/architect`, the design spec under `/docs/UX`, the review record under `/docs/SM`, and the issue template to `.github/ISSUE_TEMPLATE/work-item.md` | Each owning role | Each folder has its template, unfilled |
| 9 | Create `/docs/SM/access.md` (the access register from SOP-003), `/docs/SM/setup.md`, `/docs/SM/measures.md`, `/docs/SM/improvement-log.md`, and `/docs/SM/skills/` | Scrum Master | The files exist |
| 10 | Write the product `README.md`: what the product is, links to the four folders, how to run it locally once that exists | PM | The README links to every `/docs` folder |

### Agent configuration

| # | Item | Owner | Done when |
|---|---|---|---|
| 11 | Add `AGENTS.md` at the root: how to build, test, and run, where the docs are, and the conventions an agent must follow | Architect | A Cloud Agent given only the repository can run the tests |
| 12 | Add rules under `.cursor/rules/*.mdc`: one for the repository's conventions, one per language or framework in use | Architect | Rules are scoped with globs, not applied to everything |
| 13 | Add `.cursor/environment.json` with the install and start commands, or a Dockerfile, so a Cloud Agent starts with dependencies ready | Cloud Engineer | A Cloud Agent's run begins with a working environment and no install step |
| 14 | Add `.cursor/BUGBOT.md` with the review guidance the Code Reviewer wants Bugbot to apply | Code Reviewer | Bugbot's first review cites it |
| 15 | Add a pull request template. The one in `.github` from this repository is the default and already has the `Model` and `Classification` sections. | CTO | Every new pull request opens with the template |

### Automation and environments

| # | Item | Owner | Done when |
|---|---|---|---|
| 16 | Add CI in GitHub Actions as a check named `ci`: lint, tests, and build (C4). The build produces an artifact labeled with the commit SHA, which deployment uses. | Cloud Engineer | The ruleset requires `ci`, and a failing test blocks a merge |
| 17 | Create separate environments, at least `test` and `production`, with their own credentials and their own cloud roles. Protect `production` (C7): `shpdev-cto` and the founder as required reviewers, "Prevent self-review" on, deployment branches limited to `main`, and a production cloud role whose OIDC trust accepts only this repository's `production` environment. Because self-review is prevented, whoever starts a production deployment can't approve it, so every production deployment needs both the CTO and the founder. Add a `production-rollback` environment whose only job redeploys the last release tag, with the same two reviewers and self-review allowed, so either of them can roll back alone. | Cloud Engineer, with the founder for the protection settings | Nothing in `test` can reach `production`, a deployment to `production` waits for the other person's approval, and a rollback runs with one |
| 18 | Choose the feature flag mechanism in a decision record (C8). Flags default to off in production, flag state can be read per environment, and the production flag settings are a CODEOWNERS path. | Architect, with the Cloud Engineer | A flag can be turned on in test and stay off in production without a deployment |
| 19 | Put secrets in the secrets manager and, for Cloud Agents, in Cursor's secret store. Nothing in the repository. | Cloud Engineer | A search of the repository for tokens and keys finds none |
| 20 | Add monitoring and a cost alert for the cloud accounts the product uses | Cloud Engineer | An alert reaches the CTO and the founder |
| 21 | Write the deployment and rollback procedure in `/docs/architect/operations.md`. The pipeline deploys by commit SHA and skips a commit already live in that environment, migrations record their version, and a rollback job redeploys the last release tag. | Cloud Engineer | A release can be rolled back by following it, and re-running a deployment changes nothing |

### Tracking

| # | Item | Owner | Done when |
|---|---|---|---|
| 22 | Create labels: `owner:<role>` for each role, `tier:small`, `tier:standard`, `tier:large`, `risk:security`, `risk:privacy`, `risk:operations`, `risk:external`, `blocked`, `access`, `incident`, `escalation`, `defect`, and `escaped` | Scrum Master | The labels exist |
| 23 | Add issue templates for a work item, from `SOP/templates/issue.md`, and a defect, with the fields SOP-007 and SOP-008 need | Scrum Master | A new issue opens with the fields |
| 24 | Create the product's one GitHub Project with the states from the root README and the fields from SOP-008 | Scrum Master | The Project exists and is empty |

## Rules

1. No issue is claimed in a product repository until every checklist item is done and recorded. Partial setup is where agents burn their runs.
2. The `SOP` folder in a product repository is a copy. Changes to an SOP happen in this repository and are copied forward. A product repository may add SOPs of its own under `/docs/SM`, numbered from `SOP-101`.
3. Nothing in the checklist creates an external account or credential without the founder doing it, per SOP-003.
4. The checklist is re-run, and `/docs/SM/setup.md` updated, whenever a new environment, cloud account, or integration is added.
5. `setup.md` says plainly which controls are enforced and which are still written rules. A control is enforced only once its setting is active and has been tested once.

## Escalation

- An item that can't be completed as written is raised to the CTO with the reason and a proposed substitute. The substitute is recorded in `/docs/SM/setup.md`.
- A request to skip an item is a process change and goes through the approval gate.

## Change history

| Date | Change | Approved |
|---|---|---|
| 2026-09-24 | First draft | Pending CTO and founder |
| 2026-09-24 | SM-016: exact ruleset settings, code owners for sensitive paths, secret scanning, the `qa/verdict` status, `production` environment protection, feature flags, idempotent deployment, tier and risk labels, one persistent Project, and a rule that `setup.md` separates enforced controls from written ones | Pending CTO and founder |
| 2026-09-25 | CTO review: code owner review off and CODEOWNERS used only to request the Code Reviewer, the `qa/verdict` item removed (C5 and C6 are written controls), pull request creation limited to the founder and the CTO, the C7 consequence stated, and a `production-rollback` environment either the CTO or the founder can run alone. Items 18 to 25 renumbered 17 to 24. | Pending CTO and founder |
