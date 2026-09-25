# SOP-011: Autonomous execution

How Bots and their Cloud Agents carry work from a claimed issue to a merged pull request without being asked at every step.

| | |
|---|---|
| Owner | CTO, with the Scrum Master |
| Status | See the [SOP index](README.md). |
| Applies to | Every Bot that owns an issue, the CTO who acts on GitHub for them, and every Cloud Agent launched for them |
| Related | [Root README](../README.md) "Autonomous execution" and "Controls", [SOP-001](SOP-001-token-efficiency.md), [SOP-003](SOP-003-bot-identities-and-access.md), [SOP-005](SOP-005-cloud-agent-launch.md), [SOP-008](SOP-008-flow-queue-and-reviews.md), [SOP-009](SOP-009-escalation-and-urgent-risks.md), [SOP-010](SOP-010-releases.md) |

## Purpose

Agents work unattended, across sessions, and sometimes in parallel. Without a policy, that goes wrong in predictable ways. Two agents take the same issue. One agent overwrites another's shared change. Work is lost when a session ends. A failed agent sits unnoticed. Retries repeat comments, pull requests, or deployments. Spend runs on with nobody deciding. This SOP sets how each of those is prevented or recovered, within the identities and access SOP-003 gives. It doesn't add an approval for routine steps.

## Scope

Acting on GitHub, claiming work, isolation, progress records and handoffs, stall and failure recovery, retries and stopping, budgets, actions with external effects, and the permissions for routine, gated, and emergency actions. It doesn't cover how to write a launch prompt, which is SOP-005, or how a pull request is reviewed, which is SOP-007.

## Acting on GitHub

1. **Bots act on GitHub through the CTO.** Only the CTO (`shpdev-cto`) and the Code Reviewer (`shpdev-reviewer`) have GitHub identities, per SOP-003. Every other Bot's GitHub write is a request to the CTO, sent in the team chat with the text to post. That includes claims, labels, state changes, notes, and new issues. The CTO posts it, starting with `For <role>:`, and doesn't edit the substance. Where this SOP says an owner "posts" or "moves" something, the CTO does it on the owner's request.
2. Cloud Agents are launched by the CTO or the founder, on the owner's request, per SOP-003. A Cloud Agent pushes only its own branch, through the account connected to Cursor.
3. Other Bots read GitHub, including the Project, in the shared browser session, where GitHub is signed in as `shpdev-cto`. They only read there and never write, per SOP-003. The Scrum Master reads the Project and the issue and pull request timelines this way for the flow check and the measures. It reads usage in Cursor itself (#5).

## Claiming work

4. Work starts only from an issue in Ready that one owner has claimed. No agent is launched for an unclaimed issue, or for an issue claimed by someone else.
5. To claim an issue, the owner asks the CTO. The CTO re-reads the issue. If it already has an owner label or a claim from another role, the CTO tells the requester and stops. Otherwise it sets the `owner:<role>` label, moves the issue to In progress, and posts the claim. Because every claim goes through the CTO, two claims can't land at once.

```markdown
For <role>: claimed
Branch: <type>/<issue number>-<short-slug>
Classification: confirmed | changed, with the classification block from the root README
Budget: <the tier's budget from rule 22, or the extended one>
Touches shared: <areas from rule 8>, or none
```

6. One issue has one owner, one branch, and at most one running build agent. Review and QA runs are separate. They read the pull request and never push to its branch.
7. Only the owner hands an issue on voluntarily, with a handoff note (rule 13). The Scrum Master reassigns an issue only under rule 16.

## Isolation and shared changes

8. These are shared areas: API contracts, the database schema, shared interface components, CI and deployment configuration, `.cursor/` and `AGENTS.md`, feature flag definitions, and dependency lockfiles. A claim lists the shared areas it will touch.
9. Every Cloud Agent runs in its own cloud machine on its own branch, named per SOP-005. It never pushes to another issue's branch or to `main`.
10. If another In progress issue lists the same shared area, the two owners agree an order in the team chat, and the CTO records it on both issues before either changes the area. The shared change lands first, in its own small pull request, and the other branch merges `main` after it. If the owners can't agree in two exchanges, the Architect decides.
11. Before a pull request is marked ready, its branch is up to date with `main` and CI passes on the result. Once C4 is in place, the ruleset enforces this.

## Progress records and handoffs

12. The issue and the pull request are the durable record. A Bot's memory and an agent's transcript aren't. Anything the next session needs goes on the issue or in the pull request. The owner posts a progress note at each agent launch, at each agent result or failure, and whenever the issue is blocked. Nothing is posted on a schedule.

```markdown
For <role>: progress <date>
Attempt <n> on <model>, run <link>
Done: <one line>
Next: <one line>
Blocked by: <what>, or none
Budget used: <runs> of <limit>, <hours> of <limit>
```

13. A handoff note is a progress note with two more lines: `Open questions`, and `Don't redo`, which lists the dead ends already tried. It's posted when a session ends with the work unfinished and when the issue changes owner. The next session reads the issue, the branch or pull request, and the last handoff note before anything is launched.

## Stalls and failures

14. The Scrum Master's daily flow check (SOP-002) looks for these:
    - an issue In progress with no progress note, commit, or running agent for two working days
    - an agent run that failed, or that ran past the per-run limit in rule 22
    - a pull request with red CI for one working day
    - a review or QA request not started within the target in SOP-008
    - an issue Blocked for longer than SOP-008 allows
15. The Scrum Master sends what it finds to the owner and the CTO in the team chat. Nothing is posted on GitHub when nothing is wrong.
16. If the owner doesn't respond within one working day, the Scrum Master asks the CTO to reassign the issue. The CTO, or the founder, stops the owner's running agent, if any, and posts the reassignment with a handoff note built from the issue and the branch. The new owner continues from the branch. It starts over only if it judges the branch unusable, and it says why on the issue.
17. Before a relaunch after a failure, the CTO or the founder confirms the previous run has stopped. Two agents never push to one branch.

## Retries, escalation, and stopping

18. For the same root cause, the owner gets two attempts on the role's default model and one on its escalation model, per SOP-001 rule 8. After that, the owner stops and escalates, to the Architect for a technical cause or the PM for a requirements cause, and tells the Scrum Master. There's no fourth attempt.
19. A CI job that failed before any test ran may be re-run once. A test that fails twice is a real failure. It's never skipped, disabled, or quarantined to get to green.
20. The agent stops, and the owner escalates per SOP-009, when:
    - it needs access, an account, a secret, or infrastructure it doesn't have (SOP-003)
    - the requirements are ambiguous or contradict the code
    - the change touches a risk area the classification didn't flag. The owner adds the flag first.
    - the next step isn't a routine action in the permissions table
    - the budget is reached (rule 23)
    - it can't tell whether an earlier action took effect (rule 28)
21. Work stops, with no escalation needed, when the done criteria are met, when the CTO or the founder says stop, when an incident touches the same area (SOP-009 rule 13), or when the issue is dropped or waits for the CTO to confirm a Large classification.

## Budgets

22. Budgets are shares of the allocation included in the founder's plans. They aren't extra money, and neither a budget nor an extension ever enables on-demand spending (SOP-001 rule 18). These are starting values. The Scrum Master proposes changes from the measures, and the CTO sets them in this table with the date.

| Work | Agent runs | Active agent time | Longest single run |
|---|---|---|---|
| Build, Small issue | 4 per issue | 2 hours per issue | 45 minutes |
| Build, Standard issue | 10 per issue | 8 hours per issue | 2 hours |
| Build, Large issue | As Standard, per issue. The milestone also has a total the CTO sets when confirming the tier. | | |
| Code Reviewer | 3 per pull request | 1 hour per pull request | 30 minutes |
| QA | 3 per pull request | 2 hours per pull request | 45 minutes |

23. At the limit, the running agent finishes its current step, not mid-change, and pushes to the branch. The owner posts a handoff note and the issue moves to Blocked with the reason `budget`. The owner then asks the CTO for one extension of up to half the original budget, with the reason, or proposes splitting or reclassifying the issue. A review or QA budget that runs out goes to the CTO the same way. No answer means no extension.
24. The weekly pacing check (SOP-001 rule 19) compares the share of the allocation used with the share of the period elapsed. When usage is ahead of pace, the CTO holds new Large work or lowers the budgets of new launches until usage is back on pace. When the allocation runs out, no new agents launch, and running ones finish.

## Actions with external effects

25. Before retrying any step that has an effect outside the branch, the owner and the CTO check whether the earlier attempt already took effect, and don't repeat the step if it did. Such steps include opening a pull request, posting a review, verdict, or comment, filing an issue, deploying, running a migration, creating a cloud resource, sending a message, and calling a paid API.
26. Everything posted on GitHub for an issue carries the marker `<!-- run: <issue>-<attempt> -->` in its body. Before posting, the CTO searches for the marker.
27. Deployments and migrations run only through the pipeline, keyed to the commit. The pipeline doesn't redeploy a commit that's already live in the same environment, and migrations record their version, so a retry is safe by construction (SOP-004 item 21).
28. If nobody can tell whether a step took effect, the owner stops and escalates. Nobody retries just to be safe.

## Permissions

29. Routine actions need no approval. Gated actions go through the gate named. Emergency actions are authorized in advance for the roles named. Any Bot may ask for one at once, and whoever takes it reports it within 15 minutes on the incident issue, with the time in UTC, what was done, why, and the result.

| Kind | Action | Who does it | Gate |
|---|---|---|---|
| Routine | Claim a Ready issue within the work-in-progress limits | The CTO, on the owner's request | None |
| Routine | Launch agents within the budget | The CTO or the founder, on the owner's request | None |
| Routine | Push to the issue's own branch | The issue's Cloud Agent | None |
| Routine | Open draft and ready pull requests, and post comments, labels, notes, and state changes for an issue | The CTO as `shpdev-cto`, on the owner's request | None |
| Routine | Re-run a CI job once under rule 19 | The CTO | None |
| Routine | Deploy to the test environment | The pipeline, automatically on merge. A branch deployment or a flag change in test is run by the CTO on the owner's request. | None |
| Routine | File defects and follow-up issues, raise a tier, or add a risk flag | Anyone asks, and the CTO posts it | None |
| Gated | Approve a pull request | The Code Reviewer as `shpdev-reviewer` | The enforced review (C1 to C4) |
| Gated | Merge | The founder or the CTO | The Code Reviewer's approval on the head commit |
| Gated | Deploy to production, or turn a feature flag on in production | Started by the founder or the CTO, approved by the other | Release authorization, per SOP-010 (C7) |
| Gated | Lower a tier or remove a risk flag | The owner asks | The Architect's agreement on the issue |
| Gated | Extend a budget | The owner asks | The CTO, on the issue |
| Gated | Infrastructure that adds cost or changes security posture | The Cloud Engineer proposes it | The Architect's review and the CTO's approval |
| Gated | Change CI, rulesets, CODEOWNERS, or environment protection | The CTO proposes it in a pull request | The Code Reviewer's approval. Ruleset and environment changes are made by the founder. |
| Gated | A new external service, account, credential, or secret | Anyone asks | The founder, per SOP-003 |
| Gated | Delete data, or a branch or issue someone else owns | Anyone asks | Whoever owns it |
| Emergency | Turn a feature flag off in production when its behavior is causing harm | The CTO or the founder | Authorized in advance. Report at once. |
| Emergency | Roll production back to the last release when a SOP-010 rollback trigger fires | The CTO or the founder, alone, through the `production-rollback` environment (SOP-004 item 17) | Authorized in advance. Report at once. |
| Emergency | Stop a running agent that's doing damage | The CTO or the founder | Authorized in advance. Report at once. |

30. Every other containment action, such as rotating or revoking a credential, deleting data, or changing a cloud account, stays with the founder, directed by the CTO, per SOP-003 and SOP-009.

## Decisions and evidence

| Decision | Who decides | Evidence |
|---|---|---|
| Tier and risk flags | The owner. The CTO confirms Large, and the Architect agrees to lowering. | The classification block on the issue |
| Claiming an issue | The owner, through the CTO | The claim comment |
| Order of a shared change | The owners involved, or the Architect | A comment on both issues |
| Model escalation | The owner, per SOP-001 rule 8 | The progress note and the pull request's `Model` section |
| Stopping and escalating | The owner | The escalation comment, per SOP-009 |
| Reassigning a stalled issue | The Scrum Master asks, and the CTO does it | The reassignment comment with a handoff note |
| Budget extension | The CTO | A comment on the issue |
| An emergency action | The CTO or the founder | The incident issue's timeline |

## Escalation

- An owner who disagrees with a reassignment raises it with the Scrum Master once, then with the CTO.
- A budget that's extended twice on the same issue goes to the CTO and the PM together, with a proposal to split, reclassify, or drop the issue.
- If the flow check stops running, whoever notices tells the Scrum Master and the CTO. Until it's fixed, the Scrum Master runs it by hand each working day.
- If the CTO is unavailable, the founder does what this SOP gives the CTO. If both are unavailable, work that needs a GitHub write waits. Nobody writes as another identity.

## Change history

| Date | Change | Approved |
|---|---|---|
| 2026-09-24 | First draft, as part of SM-016 | Pending CTO and founder |
| 2026-09-25 | CTO review: Bots act on GitHub through the CTO, launches, stops, deploys, and emergency actions named for the CTO or the founder, no daily progress note, how the Scrum Master reads for the flow check, review and QA budgets capped per pull request, budgets tied to the included allocation with a weekly pacing check | Pending CTO and founder |
