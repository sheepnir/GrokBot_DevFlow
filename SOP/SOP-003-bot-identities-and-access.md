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

The identity model is a founder decision, tracked in issue #6. The founder decided it on 2026-09-24, and the CTO agrees. There are three GitHub identities: the founder's personal account, `sheepnir`, which no Bot ever uses; the CTO, `shpdev-cto`; and the Code Reviewer, `shpdev-reviewer`. No other Bot has a GitHub identity. A Cloud Agent pushes only its branch, and the CTO opens every Cloud Agent pull request as `shpdev-cto`, with a `Role` line naming the Bot that requested the work. The Code Reviewer is the required non-author reviewer.

## Scope

This SOP covers GitHub identities, cloud identities, logins on the shared cloud computer, credentials, and the access register. It doesn't cover what a Bot does with its access, which is the rest of the process.

## Current state

| Item | State | Tracked in |
|---|---|---|
| Identity model | Decided on 2026-09-24. Three GitHub identities: the founder (`sheepnir`), the CTO (`shpdev-cto`), and the Code Reviewer (`shpdev-reviewer`). Other Bots request Cloud Agent work, and the CTO or the founder launches it. A Cloud Agent pushes only its branch, through the GitHub account connected to Cursor, which is currently the founder's. The CTO opens the pull request as `shpdev-cto`, with a `Role` line naming the requesting Bot, and requests `shpdev-reviewer`. The Code Reviewer's approval is a GitHub review from `shpdev-reviewer`. | #6 |
| CTO GitHub account | `shpdev-cto`. | #6 |
| Code Reviewer GitHub account | Set up. [`shpdev-reviewer`](https://github.com/shpdev-reviewer), created by the founder, is the independent reviewer identity: separate from pull request authors, from `shpdev-cto`, and from the founder's personal account. Its login is the 1Password item "GitHub - ShpDev - Code Reviewer". Its profile picture is the team portrait `assets/team/code-reviewer.png`. It's a collaborator with Write on this repository, so its approvals count toward branch protection. No product repository exists yet. | #6 |
| Other Bots' GitHub accounts | None, by design. The PM, Designer, Architect, Scrum Master, Frontend, Backend, and Cloud Engineers, and QA work through the CTO. | #6 |
| Founder's personal GitHub account | `sheepnir`. Never used by a Bot. | |
| Pull request creation on `main` | Restricted by ruleset to the founder and the CTO. | #6 |
| Scrum Master read access to Cursor usage | Set up on 2026-09-24. The Scrum Master reads usage in Cursor itself. | #5 |
| Cursor on-demand monthly limit | Disabled on 2026-09-24. Enabling it needs the founder, per SOP-001 rule 18. | #4 |

The SOP index records that Bot identities have been in place since 2026-09-24 (#6), so SOP-001 rules 1 through 6 are in full effect. Where those rules say a Bot launches a Cloud Agent or opens a pull request, the Bot requests it and the CTO does it.

## Requirements for the identity model

The decided model satisfies all of these, and any future change to it must too:

1. **The author and the reviewer are different identities.** The GitHub identities are the founder, the CTO, and the Code Reviewer, and no Bot ever uses the founder's. The root README's definition of done requires a reviewer other than the author, so the CTO never counts as the reviewer on a pull request it opened, and the Code Reviewer's approval is required on every pull request the CTO opens. That includes documentation pull requests for the PM, Designer, Architect, and Scrum Master, which the Code Reviewer or the founder reviews. On a pull request the Code Reviewer opened, the founder or the CTO is the reviewer.
2. **A Cloud Agent's pull request is opened by the CTO and attributed to the role that requested it.** The Cloud Agent pushes only its branch. The CTO opens every Cloud Agent pull request as `shpdev-cto`, and the pull request template's `Role` line and `Model` section name the requesting role and the models that produced the work.
3. **Least privilege.** Each identity gets the access in the table below and nothing more. Access is granted per repository, never organization-wide.
4. **No account is created before the founder approves it**, and the founder creates it. Bots don't create accounts, tokens, or keys.
5. **Credentials live in 1Password**, in a vault the founder controls. They're never in a profile, a skill, a routine, a chat, a Bot's memory, a repository, or a file on the shared computer.

## Access by role

| Role | GitHub | Cloud | Cursor |
|---|---|---|---|
| CTO | Admin on the process repository, maintain on product repositories | Read-only on billing and cost views | Team admin, if a team plan is used |
| Product Manager, UX/UI Designer, Software Architect | None. Works through the CTO. CODEOWNERS names the owning role's `/docs` folder for review purposes. | None | Cloud Agents on their assigned models, launched by the CTO or the founder |
| Scrum Master | None. Works through the CTO. | None | Read-only usage in Cursor (#5) |
| Frontend and Backend Engineers | None. Works through the CTO. Their Cloud Agents push feature branches only, never `main`. | None. Engineers use environments the Cloud Engineer provides. | Cloud Agents on Composer 2.5 and Grok 4.7, launched by the CTO or the founder |
| Cloud Engineer | None. Works through the CTO. Its Cloud Agents push feature branches only. GitHub Actions secrets are set by the founder on request. | OIDC-assumed roles per environment, scoped to that environment. No long-lived access keys. Production roles need a per-change approval. | Cloud Agents on Composer 2.5 and Grok 4.7, launched by the CTO or the founder |
| Code Reviewer | Write, so its review can approve, but no merge. Never pushes to a branch it's reviewing. | None | Cloud Agents on its ordered list |
| QA Engineer | None. Works through the CTO, who posts its verdicts and files its defects. | Read-only on test environments | Cloud Agents on its ordered list, launched by the CTO or the founder |

Bots don't hold personal access tokens that outlive a task. Where a token is unavoidable, it's fine-grained, scoped to one repository, expires within 90 days, and is recorded in the access register.

## The shared cloud computer

Every Bot on the account shares one cloud computer. Browser logins, files, and terminal state on it are visible to every Bot. So:

6. Only services the whole team may use are logged in on the shared computer: the product repositories, the issue tracker, the design tool, and test environments.
7. Billing consoles, cloud account root or admin consoles, the password manager, the founder's email, and anything with personal data are never logged in there. Those actions are the founder's, on the founder's own devices.
8. A Bot signs out of any service it logged into for a one-off task, and says so in the task's record.
9. Files a Bot keeps on the shared computer go under a folder named for its role. Nothing under any role's folder is a secret. A Bot that finds one tells the founder and doesn't copy or use it.

Bots also share one browser profile. A Bot with its own GitHub account, which today means the Code Reviewer, adds it with GitHub's account switcher and never signs out `shpdev-cto`. It confirms its own account is the active one before any GitHub write, and switches back when done. Its login is filled from 1Password without the Bot ever seeing it, so the password manager itself is never logged in on the shared computer (rule 7). GitHub is a team service under rule 6, so this login isn't a one-off under rule 8.

## How identities support the controls

The root README lists the controls that protect `main` and production. With three GitHub identities, this is what each control can rely on:

- **Independent approval (C2): enforced.** Every Cloud Agent pull request is opened by `shpdev-cto` and pushed through the account connected to Cursor, currently `sheepnir`. GitHub never counts an author's own approval, and the ruleset requires approval from someone other than the last pusher. So on those pull requests, `shpdev-reviewer` is the only identity whose approval counts. That's the control.
- **QA verdicts tied to a commit (C5): written.** QA has no identity, and any writer could set a commit status, so a status would prove no more than a comment. The CTO posts QA's verdict verbatim, naming the commit, and the Code Reviewer checks that it names the head commit before approving.
- **Specialist review (C6): written.** The Architect and the Cloud Engineer have no identities, so code owner review can't require them. Making the CTO a required code owner would block every pull request the CTO opens. The CTO posts the specialist's review on the pull request, and the Code Reviewer doesn't approve without it.
- **Production deployment (C7): enforced.** The `production` environment's required reviewers are `shpdev-cto` and `sheepnir`, with self-review prevented, so whoever starts a production deployment can't approve it. Every production deployment therefore involves both the CTO and the founder (SOP-010 rule 6). A rollback uses a separate environment that either of them can run alone (SOP-004 item 17).

A Bot other than the CTO and the Code Reviewer never writes to GitHub, including through the shared browser session signed in as `shpdev-cto`. It may read there. Every write it needs is a request to the CTO, per SOP-011 rule 1.

## Requesting, recording, and reviewing access

10. A Bot that needs access it doesn't have opens an issue labeled `access`, stating what it needs, for which task, and for how long. It doesn't work around the gap.
11. The CTO approves or declines on the issue. The founder provisions approved access.
12. Every grant is recorded in the product repository's access register at `/docs/SM/access.md`: identity, what was granted, scope, date, approver, and review date.
13. The Scrum Master reviews the register once a quarter, in the measures routine on the first working day of the quarter. Access that no open work needs is removed by the founder.
14. When a role changes hands, its identity keeps the role's access and loses nothing else. Identities belong to roles, not to Bot instances.

## Cloud Agent access

15. Cloud Agents reach secrets only through the Cursor dashboard's secret store, never through the repository or the prompt.
16. Cloud roles for agents are assumed through OIDC, per Cursor's guidance, not through stored keys.
17. If network restrictions are on for Cloud Agents, the Cloud Engineer keeps the allowlist in `.cursor/environment.json` and adds hosts through a pull request.

## Exposure

18. A Bot that sees a credential where it shouldn't be, or suspects one has leaked, stops the task, tells the founder and the CTO at once, and doesn't try to fix it by using or moving the credential.
19. The founder rotates the credential. The CTO records the exposure and the rotation in `/docs/architect/decisions/` as a security note, with no secret in it, because repositories are public.

## Escalation

- Access denied on an issue can be raised once to the CTO with the task it blocks. The CTO's second answer stands until something about the task changes.
- A disagreement over a role's default access row is a process change to this SOP.

## Change history

| Date | Change | Approved |
|---|---|---|
| 2026-09-24 | First draft | Pending CTO and founder |
| 2026-09-24 | CTO review: the founder or the CTO reviews documentation pull requests from a shared identity | Pending CTO and founder |
| 2026-09-24 | Current state records the CTO's account, the Code Reviewer's account `shpdev-reviewer` as set up, and the founder's personal account, and how a Bot's account shares the browser profile (factual). Process change: the Code Reviewer's approval is a GitHub review from `shpdev-reviewer` instead of a comment | Pending CTO and founder |
| 2026-09-24 | Identity model decided (#6): three GitHub identities (founder, CTO, Code Reviewer); the CTO opens every Cloud Agent pull request; the Code Reviewer is the required non-author reviewer | CTO and founder, 2026-09-24 |
| 2026-09-24 | SM-016: how the three identities support the controls in the root README, with C5 and C6 as written controls and the C7 consequence stated; other Bots read GitHub but never write to it; access reviews moved to the quarterly measures routine; the on-demand row updated to match SOP-001 (#4) | Pending CTO and founder |
