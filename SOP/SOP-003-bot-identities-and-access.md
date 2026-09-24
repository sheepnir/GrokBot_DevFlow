# SOP-003: Bot identities and access

Who a Bot is on GitHub and in the cloud, and what it may reach.

| | |
|---|---|
| Owner | CTO |
| Status | See the [SOP index](README.md). |
| Applies to | Every Bot, every Cloud Agent a Bot launches, and the founder and CTO who provision access |
| Related | [Root README](../README.md), [SOP-001](SOP-001-token-efficiency.md) prerequisites, issues [#6](https://github.com/sheepnir/GrokBot_DevFlow/issues/6), [#5](https://github.com/sheepnir/GrokBot_DevFlow/issues/5), and [#4](https://github.com/sheepnir/GrokBot_DevFlow/issues/4), [Cloud Agent best practices](https://cursor.com/docs/cloud-agent/best-practices) |

## Purpose

The process depends on two things that only identities can give it. A pull request must be reviewed by someone other than its author, so the Code Reviewer needs an identity separate from the engineers. And a Bot must be able to act only within its role, so each identity needs the least access that its role requires. This SOP sets the rules any identity model must satisfy, the access each role gets, how access is requested and recorded, and what happens when a credential is exposed.

The identity model itself is a founder decision, tracked in issue #6. This SOP records the decision once it's made.

## Scope

This SOP covers GitHub identities, cloud identities, logins on the shared cloud computer, credentials, and the access register. It doesn't cover what a Bot does with its access, which is the rest of the process.

## Current state

| Item | State | Tracked in |
|---|---|---|
| Identity model | Not yet decided. Until then, the founder opens pull requests and launches Cloud Agents on a Bot's behalf, and the Code Reviewer's approval is recorded as a comment rather than a GitHub review. | #6 |
| Pull request creation on `main` | Restricted by ruleset to the founder and the CTO. Bot identities are added to the ruleset when they exist. | #6 |
| Scrum Master read access to Cursor usage | Not set up. The founder posts the numbers. | #5 |
| Cursor on-demand monthly limit | Not yet set | #4 |

When the identity model is in place, the SOP index records the date, and SOP-001 rules 1 through 6 take full effect.

## Requirements for the identity model

Whatever model the founder chooses, it satisfies all of these:

1. **One identity per role that authors or reviews.** The Frontend, Backend, and Cloud Engineers, the Code Reviewer, and QA each act under a distinct identity, so GitHub can tell an author from a reviewer. The PM, Designer, Architect, and Scrum Master may share one documentation identity if their work never needs review by one another under this process.
2. **A Cloud Agent's pull request is attributed to the role that launched it.** If the platform authors every pull request through one connected account, the pull request template's `Model` section and a `Role` line make the launching role explicit, and the Code Reviewer's identity is still distinct.
3. **Least privilege.** Each identity gets the access in the table below and nothing more. Access is granted per repository, never organization-wide.
4. **No account is created before the founder approves it**, and the founder creates it. Bots don't create accounts, tokens, or keys.
5. **Credentials live in 1Password**, in a vault the founder controls. They're never in a profile, a skill, a routine, a chat, a Bot's memory, a repository, or a file on the shared computer.

## Access by role

| Role | GitHub | Cloud | Cursor |
|---|---|---|---|
| CTO | Admin on the process repository, maintain on product repositories | Read-only on billing and cost views | Team admin, if a team plan is used |
| Product Manager, UX/UI Designer, Software Architect | Write, scoped by CODEOWNERS to their `/docs` folder | None | Cloud Agents on their assigned models |
| Scrum Master | Triage, plus admin on GitHub Projects | None | Read-only usage, once #5 is done |
| Frontend and Backend Engineers | Write on feature branches. No direct push to `main`. | None. Engineers use environments the Cloud Engineer provides. | Cloud Agents on Composer 2.5 and Grok 4.7 |
| Cloud Engineer | Write on feature branches, plus GitHub Actions secrets by request | OIDC-assumed roles per environment, scoped to that environment. No long-lived access keys. Production roles need a per-change approval. | Cloud Agents on Composer 2.5 and Grok 4.7 |
| Code Reviewer | Write, so its review can approve, but no merge. Never pushes to a branch it's reviewing. | None | Cloud Agents on its ordered list |
| QA Engineer | Triage, so it can label and file defects | Read-only on test environments | Cloud Agents on its ordered list |

Bots don't hold personal access tokens that outlive a task. Where a token is unavoidable, it's fine-grained, scoped to one repository, expires within 90 days, and is recorded in the access register.

## The shared cloud computer

Every Bot on the account shares one cloud computer. Browser logins, files, and terminal state on it are visible to every Bot. So:

6. Only services the whole team may use are logged in on the shared computer: the product repositories, the issue tracker, the design tool, and test environments.
7. Billing consoles, cloud account root or admin consoles, the password manager, the founder's email, and anything with personal data are never logged in there. Those actions are the founder's, on the founder's own devices.
8. A Bot signs out of any service it logged into for a one-off task, and says so in the task's record.
9. Files a Bot keeps on the shared computer go under a folder named for its role. Nothing under any role's folder is a secret. A Bot that finds one tells the founder and doesn't copy or use it.

## Requesting, recording, and reviewing access

10. A Bot that needs access it doesn't have opens an issue labeled `access`, stating what it needs, for which task, and for how long. It doesn't work around the gap.
11. The CTO approves or declines on the issue. The founder provisions approved access.
12. Every grant is recorded in the product repository's access register at `/docs/SM/access.md`: identity, what was granted, scope, date, approver, and review date.
13. The Scrum Master reviews the register at the last retrospective of every quarter. Access that no open work needs is removed by the founder.
14. When a role changes hands, its identity keeps the role's access and loses nothing else. Identities belong to roles, not to Bot instances.

## Cloud Agent access

15. Cloud Agents reach secrets only through the Cursor dashboard's secret store, never through the repository or the prompt.
16. Cloud roles for agents are assumed through OIDC, per Cursor's guidance, not through stored keys.
17. If network restrictions are on for Cloud Agents, the Cloud Engineer keeps the allowlist in `.cursor/environment.json` and adds hosts through a pull request.

## Exposure

18. A Bot that sees a credential where it shouldn't be, or suspects one has leaked, stops the task, tells the founder and the CTO at once, and doesn't try to fix it by using or moving the credential.
19. The founder rotates the credential. The CTO records the exposure and the rotation in `/docs/architect/decisions/` as a security note, with no secret in it, because repositories are public.

## Escalation

- Access denied on an issue can be raised once to the CTO with the task it blocks. The CTO's second answer is final for that sprint.
- A disagreement over a role's default access row is a process change to this SOP.

## Change history

| Date | Change | Approved |
|---|---|---|
| 2026-09-24 | First draft | Pending CTO and founder |
