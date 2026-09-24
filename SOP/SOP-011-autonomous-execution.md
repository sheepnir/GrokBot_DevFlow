# SOP-011: Autonomous execution

How Bots and their Cloud Agents carry work from a claimed issue to a merged pull request without being asked at every step.

| | |
|---|---|
| Owner | CTO, with the Scrum Master |
| Status | See the [SOP index](README.md). |
| Applies to | Every Bot that owns an issue, and every Cloud Agent it launches |
| Related | [Root README](../README.md) "Autonomous execution" and "Controls", [SOP-001](SOP-001-token-efficiency.md), [SOP-003](SOP-003-bot-identities-and-access.md), [SOP-005](SOP-005-cloud-agent-launch.md), [SOP-008](SOP-008-flow-queue-and-reviews.md), [SOP-009](SOP-009-escalation-and-urgent-risks.md), [SOP-010](SOP-010-releases.md) |

## Purpose

Agents work unattended, across sessions, and sometimes in parallel. Without a policy, that goes wrong in predictable ways. Two agents take the same issue. One agent overwrites another's shared change. Work is lost when a session ends. A failed agent sits unnoticed. Retries repeat comments, pull requests, or deployments. Spend runs on with nobody deciding. This SOP sets how each of those is prevented or recovered. It doesn't add an approval for routine steps.

## Scope

Claiming work, isolation, progress records and handoffs, stall and failure recovery, retries and stopping, budgets, actions with external effects, and the permissions for routine, gated, and emergency actions. It doesn't cover how to write a launch prompt, which is SOP-005, or how a pull request is reviewed, which is SOP-007.

## Claiming work

1. Work starts only from an issue in Ready that one owner has claimed. A Bot never launches an agent for an unclaimed issue, or for an issue claimed by someone else.
2. To claim an issue, the Bot re-reads it first. If it already has an owner label, an assignee, or a claim comment from another role, the Bot stops. Otherwise it sets the `owner:<role>` label, and the assignee where the role has a GitHub identity, moves the issue to In progress, and posts the claim:

```markdown
Claimed by: <role>
Branch: <type>/<issue number>-<short-slug>
Classification: confirmed | changed, with the classification block from the root README
Budget: <the tier's budget from rule 21, or the extended one>
Touches shared: <areas from rule 7>, or none
```

3. If two claims collide, the earlier claim comment wins. The later Bot withdraws its claim in a comment and stops any agent it launched. The Scrum Master settles anything unclear.
4. One issue has one owner, one branch, and at most one running build agent. Review and QA runs are separate. They read the pull request and never push to its branch.
5. Only the owner hands an issue on voluntarily, with a handoff note (rule 13). The Scrum Master reassigns an issue only under rule 15.

## Isolation and shared changes

6. Every Cloud Agent runs in its own cloud machine on its own branch, named per SOP-005. It never pushes to another issue's branch or to `main`.
7. These are shared areas: API contracts, the database schema, shared interface components, CI and deployment configuration, `.cursor/` and `AGENTS.md`, feature flag definitions, and dependency lockfiles. A claim lists the shared areas it will touch.
8. If another In progress issue lists the same shared area, the two owners agree an order in a comment on both issues before either changes it. The shared change lands first, in its own small pull request, and the other branch merges `main` after it. If they can't agree in two exchanges, the Architect decides.
9. Before a pull request is marked ready, its branch is up to date with `main` and CI passes on the result. Once C4 is in place, the ruleset enforces this.

## Progress records and handoffs

10. The issue and the pull request are the durable record. A Bot's memory and an agent's transcript aren't. Anything the next session needs goes on the issue or in the pull request.
11. The owner posts a progress note at each agent launch, at each agent result or failure, whenever the issue is blocked, and at least once each working day it stays In progress:

```markdown
Progress <date>: attempt <n> on <model>, run <link>
Done: <one line>
Next: <one line>
Blocked by: <what>, or none
Budget used: <runs> of <limit>, <hours> of <limit>
```

12. The pull request opens as a draft on the first push, so the work is visible and CI runs early.
13. A handoff note is a progress note with two more lines: `Open questions`, and `Don't redo`, which lists the dead ends already tried. It's posted when a session ends with the work unfinished and when the issue changes owner. The next session reads the issue, the pull request, and the last handoff note before launching anything.

## Stalls and failures

14. The Scrum Master's daily flow check (SOP-002) flags these on the issue:
    - an issue In progress with no progress note or commit for one working day
    - an agent run that failed, or that ran past the per-run limit in rule 21
    - a pull request with red CI for one working day
    - a review or QA request not started within the target in SOP-008
    - an issue Blocked for longer than SOP-008 allows
15. When something is flagged, the owner gets one working day to respond on the issue. If the owner doesn't respond, the Scrum Master stops the owner's running agent, if any, and reassigns the issue with a handoff note built from the issue and the branch. The new owner continues from the branch. It starts over only if it judges the branch unusable, and it says why on the issue.
16. Before relaunching after a failure, the owner confirms the previous run has stopped. Two agents never push to one branch.

## Retries, escalation, and stopping

17. For the same root cause, the owner gets two attempts on the role's default model and one on its escalation model, per SOP-001 rule 8. After that, the owner stops and escalates, to the Architect for a technical cause or the PM for a requirements cause, and tells the Scrum Master. There's no fourth attempt.
18. A CI job that failed before any test ran may be re-run once. A test that fails twice is a real failure. It's never skipped, disabled, or quarantined to get to green.
19. The agent stops, and the owner escalates per SOP-009, when:
    - it needs access, an account, a secret, or infrastructure it doesn't have (SOP-003)
    - the requirements are ambiguous or contradict the code
    - the change touches a risk area the classification didn't flag. The owner adds the flag first.
    - the next step isn't a routine action in the permissions table
    - the budget is reached (rule 22)
    - it can't tell whether an earlier action took effect (rule 27)
20. Work stops, with no escalation needed, when the done criteria are met, when the CTO or the founder says stop, when an incident touches the same area (SOP-009 rule 13), or when the issue is dropped or waits for the CTO to confirm a Large classification.

## Budgets

21. Each issue has a budget. These are starting values. The Scrum Master proposes changes from the measures, and the CTO sets them in this table with the date.

| Tier | Build agent runs per issue | Active agent time per issue | Longest single run |
|---|---|---|---|
| Small | 4 | 2 hours | 45 minutes |
| Standard | 10 | 8 hours | 2 hours |
| Large | As Standard, per issue. The milestone also has a total the CTO sets when confirming the tier. | | |

Review and QA runs aren't charged to the owner's budget. Each gets two runs per commit under review.

22. At the limit, the running agent finishes its current step, not mid-change, and pushes to the branch. The owner posts a handoff note and moves the issue to Blocked with the reason `budget`. The owner then asks the CTO for one extension of up to half the original budget, with the reason, or proposes splitting or reclassifying the issue. No answer means no extension.
23. The account-wide limit is SOP-001 rule 18. When it's reached, no new agents launch. Running ones finish.

## Actions with external effects

24. Before retrying any step that has an effect outside the branch, the owner checks whether the earlier attempt already took effect, and doesn't repeat the step if it did. Such steps include opening a pull request, posting a review, verdict, or comment, filing an issue, deploying, running a migration, creating a cloud resource, sending a message, and calling a paid API.
25. Everything an agent or a Bot creates on GitHub carries the marker `<!-- run: <issue>-<attempt> -->` in its body. Before creating anything, the owner searches for the marker.
26. Deployments and migrations run only through the pipeline, keyed to the commit. The pipeline doesn't redeploy a commit that's already live in the same environment, and migrations record their version, so a retry is safe by construction (SOP-004 item 22).
27. If the owner can't tell whether a step took effect, it stops and escalates. It never retries just to be safe.

## Permissions

28. Routine actions need no approval. Gated actions go through the gate named. Emergency actions are authorized in advance for the roles named, and are reported within 15 minutes on the incident issue, with the time in UTC, what was done, why, and the result.

| Kind | Action | Who | Gate |
|---|---|---|---|
| Routine | Claim a Ready issue within the work-in-progress limits | Any owner | None |
| Routine | Create a branch, push to it, open draft and ready pull requests through the identity SOP-003 gives | The owner | None |
| Routine | Comment, label, and move the owner's own issue between states | The owner | None |
| Routine | Launch agents within the budget, run tests, and re-run a CI job once under rule 18 | The owner | None |
| Routine | Deploy to the test environment through the pipeline, and turn feature flags on or off in test | The owner | None |
| Routine | File defects and follow-up issues, raise a tier, or add a risk flag | Anyone | None |
| Gated | Merge | An identity with merge rights | The enforced review (C1 to C4) |
| Gated | Deploy to production, or turn a feature flag on in production | Cloud Engineer, through the pipeline | Release authorization, per SOP-010 (C7) |
| Gated | Lower a tier or remove a risk flag | The owner | The Architect's agreement on the issue |
| Gated | Extend a budget | The owner asks | The CTO, on the issue |
| Gated | Infrastructure that adds cost or changes security posture | Cloud Engineer | The Architect's review and the CTO's approval |
| Gated | Change CI, rulesets, CODEOWNERS, or environment protection | CTO or Cloud Engineer | A reviewed pull request. Ruleset and environment changes are made by the founder. |
| Gated | A new external service, account, credential, or secret | Requested by anyone | The founder, per SOP-003 |
| Gated | Delete data, or a branch or issue someone else owns | Requested by anyone | Whoever owns it |
| Emergency | Turn a feature flag off in production when its behavior is causing harm | The owner, the Cloud Engineer, or the CTO | Authorized in advance. Report at once. |
| Emergency | Roll production back to the last released commit when a SOP-010 rollback trigger fires | The Cloud Engineer runs the pipeline's rollback job | Authorized in advance. The CTO or the founder approves the environment gate on request, without re-review. |
| Emergency | Stop a running agent that's doing damage | Any Bot | Authorized in advance. Report at once. |

29. Every other containment action, such as rotating or revoking a credential, deleting data, or changing a cloud account, stays with the founder, directed by the CTO, per SOP-003 and SOP-009.

## Decisions and evidence

| Decision | Who decides | Evidence |
|---|---|---|
| Tier and risk flags | The owner. The CTO confirms Large, and the Architect agrees to lowering. | The classification block on the issue |
| Claiming an issue | The owner | The claim comment |
| Order of a shared change | The owners involved, or the Architect | A comment on both issues |
| Model escalation | The owner, per SOP-001 rule 8 | The progress note and the pull request's `Model` section |
| Stopping and escalating | The owner | The escalation comment, per SOP-009 |
| Reassigning a stalled issue | The Scrum Master | The reassignment comment with a handoff note |
| Budget extension | The CTO | A comment on the issue |
| An emergency action | The role named in the permissions table | The incident issue's timeline |

## Escalation

- An owner who disagrees with a reassignment raises it with the Scrum Master once, then with the CTO.
- A budget that is extended twice on the same issue goes to the CTO and the PM together, with a proposal to split, reclassify, or drop it.
- If the flow check itself stops running, whoever notices tells the Scrum Master and the CTO. Until it's fixed, the Scrum Master runs it by hand each working day.

## Change history

| Date | Change | Approved |
|---|---|---|
| 2026-09-24 | First draft, as part of SM-016 | Pending CTO and founder |
