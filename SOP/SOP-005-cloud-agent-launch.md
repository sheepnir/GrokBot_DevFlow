# SOP-005: Cloud Agent launch

The checklist a Bot follows to delegate work to a Cursor Cloud Agent.

| | |
|---|---|
| Owner | Software Architect |
| Status | See the [SOP index](README.md). |
| Applies to | Every Bot that launches a Cloud Agent |
| Related | [SOP-001](SOP-001-token-efficiency.md), [SOP-003](SOP-003-bot-identities-and-access.md), [SOP-007](SOP-007-pull-request-review-and-qa.md), [Cloud Agents](https://cursor.com/docs/cloud-agent), [Cloud Agent best practices](https://cursor.com/docs/cloud-agent/best-practices) |

## Purpose

SOP-001 says Bots coordinate and Cloud Agents produce. This SOP says how the handoff happens: what a launch must contain, what the agent is expected to return, and what the Bot does with the result. A launch that leaves any of this out costs a second launch.

## Scope

This SOP covers launching a Cloud Agent for any repository task: a document, code, tests, a review, a verification run, or an infrastructure change. It doesn't cover which model to use, which is SOP-001, or how the resulting pull request is reviewed, which is SOP-007.

## Before launching

1. The task has an issue with one accountable owner, and that owner is the launching Bot's role. Review and QA requests are the exception: they target a pull request. QA requests a launch through the CTO; the Code Reviewer may launch a registered review run under rule 7.
2. The owner has claimed the issue per SOP-011, and the issue has a classification and budget left.
3. The Bot has read the issue and the documents it links, and can state the expected output in one sentence. If it can't, the issue isn't ready, and the Bot says so on the issue rather than launching.
4. The Bot has no other Cloud Agent running for a different issue. One issue at a time, per the root README.

## The launch

Every launch prompt has these parts, in this order. A Bot keeps them short and links rather than pastes.

```markdown
Issue: <link>
Role: <role requesting this work>
Tier: <Small | Standard | Large>, risk flags: <flags or none>
Model: <model from SOP-001 for this role and this attempt>
Attempt: <n>, budget left: <runs>, <hours>
Dispatcher: <registered session ID>
Save deadline: <UTC time>, hard stop: <UTC time>
Timeout: <tested platform timeout | independent watchdog | named supervisor>
Branch: <type>/<issue number>-<short-slug>   (type is one of docs, feat, fix, infra, test)
Operation ledger: <record location; dispatcher assigns a separate action and UUID per external operation>
Marker format: <!-- operation: <repo>-<issue>-<action>-<UUID> --> (reuse only for a retry of that operation)

Task
One paragraph. What to produce, and for whom.

Context
- Requirements: <link to the BRD section or requirement IDs>
- Design: <link to the design spec, if any>
- Architecture: <link to the TDD section, if any>
- Conventions: AGENTS.md and .cursor/rules in the repository

Done when
- <the acceptance criteria from the issue, verbatim>
- Tests that cover the change pass, and the full suite passes once before the pull request opens
- The change and its tests are pushed to the branch above. Push only that branch. The CTO opens the pull request as `shpdev-cto`, with the template filled in, including the Classification, Role, and Model sections.

Constraints
- Change only what the task needs. Note anything else you find on the issue instead.
- No new accounts, credentials, infrastructure, or external services. Stop and report if the task seems to need one.
- Don't install what .cursor/environment.json already provides.
- Before retrying anything that has an effect outside the branch, check whether it already happened (SOP-011 rule 25).
- If this is unfinished work that will merge, keep it behind the feature flag named on the issue.
```

For a document task, "Done when" names the file, its folder under `/docs`, and the template it follows from SOP-006, and the pull request goes to that folder's owner for acceptance.

5. The model in the launch is the one SOP-001 assigns to the role for this attempt. On the third attempt at the same root cause, it's the escalation model, and the issue says so.
6. Branch names use the pattern above. One branch per issue. A second pull request for the same issue reuses the branch after the first merges.
7. The CTO or the founder launches the agent through the designated dispatcher, on the owning Bot's request, per SOP-003 and SOP-011 rules 2 and 5. The dispatcher verifies the timeout or named supervisor before launch; an unattended run without a tested stop mechanism does not start. The owning Bot sends the launch prompt to the CTO. The CTO launches it from the Cursor web app, the desktop app, Slack, or the API, and posts the prompt on the issue. The Code Reviewer may launch its own review runs after registering the run and its timeout with the dispatcher; it does not claim build work or push changes.

## While the agent runs

8. The CTO posts a progress note for the owner, with the run link, as soon as it has one, per SOP-011 rule 12.
9. The owning Bot does not repeatedly poll for progress. It picks up the result when the agent reports. The independent watchdog or named supervisor still enforces the deadlines under SOP-011 rule 17; result notifications do not replace timeout enforcement.
10. If the agent asks a question, the Bot answers from the issue and the linked documents. If the answer isn't there, the Bot asks the owning role of the missing document, once, and records the answer on the issue.

## When the agent finishes

11. The owning Bot reads the branch's diff, or the pull request once it's open, not just the agent's summary, and checks it against "Done when". A pull request that doesn't meet it goes back to the agent with the gap named, on the same branch.
12. The owning Bot gives the CTO every model used, for the `Model` section. The CTO opens the pull request, or marks it ready, and moves the issue to In review.
13. The CTO posts a progress note for the owner with the pull request link, the models used, and anything the agent noted that's outside the task. Those notes become new issues if they're worth doing.
14. A failed run is recorded in a progress note with the root cause in one line. Two failures with the same root cause trigger SOP-001 rule 8, and the limits in SOP-011 rule 18 apply after that.

## Rules for reviews and verification runs

15. The Code Reviewer's and QA's launches follow the same shape. "Done when" is the review findings or the verdict, in the format SOP-007 requires, posted on the pull request.
16. The Reviewer's launch never pushes to the branch under review. Fixes are the author's.

## Escalation

- An agent that reports it needs access, an account, or infrastructure stops, and the Bot opens an `access` issue per SOP-003.
- An agent that finds the task can't be done as specified stops, and the Bot takes it to the owning role of the document that's wrong, then to the Scrum Master if the queue or a milestone is affected.

## Change history

| Date | Change | Approved |
|---|---|---|
| 2026-09-24 | First draft | Pending CTO and founder |
| 2026-09-24 | SM-016: launches follow a claim instead of a sprint, the prompt carries the tier, attempt, budget, and run marker, the pull request opens as a draft on the first push, and results are recorded as progress notes | Pending CTO and founder |
| 2026-09-25 | CTO review, building on SM-014: the `Role` line names the requesting role, the agent pushes only its branch and the CTO opens the pull request, and the CTO or the founder launches the agent | Pending CTO and founder |
| 2026-09-26 | SM-017: align runtime, coordination, recovery, and rollout instructions with the activation checklist | Pending CTO and founder |
