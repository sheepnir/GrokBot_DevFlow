# SOP-007: Pull request, review, and QA

What the author, the Code Reviewer, QA, and the specialists each do on a pull request, scaled to the issue's tier and risk flags.

| | |
|---|---|
| Owner | Code Reviewer, with the QA Engineer |
| Status | See the [SOP index](README.md). |
| Applies to | Every role that opens, reviews, verifies, or merges a pull request |
| Related | [Root README](../README.md) "Classify the work first", "Controls", and SM-003, [SOP-001](SOP-001-token-efficiency.md) rules 4 to 6, [SOP-005](SOP-005-cloud-agent-launch.md), the [pull request template](../.github/pull_request_template.md), [Bugbot](https://cursor.com/docs/bugbot) |

## Purpose

The root README says every pull request gets an independent review, that QA and specialists join where the tier or a risk flag requires them, and that the Reviewer gives final merge approval for an exact commit once every required check has passed. This SOP turns that into a sequence each role can follow, with the formats their outputs take.

## Scope

Every pull request in a product repository, code or documentation. What each pull request needs depends on its issue:

| Issue | Code Reviewer | QA verdict | Specialist review |
|---|---|---|---|
| Small, no risk flags | Required | Only if the Reviewer or the owner asks for one | None |
| Standard or Large | Required | Required | Where the tier table says so |
| Any tier with `risk:security` or `risk:privacy` | Required | Required, on the frontier model of QA's family | The Architect's security review |
| Any tier with `risk:operations` | Required | As for the tier | The Cloud Engineer's review |
| Documentation only | Required | Only if the document owner asks for one | The document owner's acceptance |

## The author

1. A pull request covers one issue. An issue may take several pull requests, such as a shared change first (SOP-011 rule 10) or work that merges behind a feature flag. Small PRs are preferred: target a soft cap of ~400 changed lines, excluding tests, fixtures, and generated files. A larger PR needs a split plan recorded in the issue before build.
2. The author fills every section of the pull request template. The `Model` section lists every model that produced a commit, per SOP-001 rule 4, and a `Role` line always names the requesting role, because the CTO opens every Cloud Agent pull request as `shpdev-cto`, per SOP-003.
3. The pull request title starts with the requirement IDs it implements, for example `REQ-AUTH-003: password reset flow`, or with the issue number when there are none, for example `#42: invoice dates in the user's time zone`.
4. Test evidence is the author's: what was run, where the output is, and what it shows. "Tests pass" without a link isn't evidence. A bug fix includes a test that fails without the fix. Never claim a build or test passed on a platform the agent can't run; state what wasn't tested.
5. The CTO opens the pull request as `shpdev-cto` once "Done when" from the launch is met, or earlier as a draft if the owner wants early CI or feedback, and requests review from `shpdev-reviewer`. Marking it ready is the signal to the Reviewer, and to QA and the specialists where required. The issue moves to In review.
6. If the issue isn't Done when this pull request merges, the pull request names the feature flag that keeps the unfinished behavior off in production, per SOP-010.
7. The author fixes blocking findings and QA failures on the same branch, replies on each thread with the commit that addresses it, and re-requests review. The author resolves a thread only after the Reviewer or QA confirms. Review fixes go on the same branch, never a new branch.
8. The author never approves, dismisses a review, or merges before the Reviewer's final approval. The authors are every role whose request produced a commit on the pull request, the identity that opened it, and every identity that pushed a commit to it. On a Cloud Agent pull request, that's the requesting roles, `shpdev-cto`, and the account connected to Cursor, currently `sheepnir`. With the ruleset from SOP-004, `shpdev-reviewer` is then the only identity whose approval counts.
9. Before marking a pull request ready for review, ensure the PR body's test counts, file list, and Model section match the head commit.

## Stacked pull requests

10. When an issue requires multiple PRs where later PRs depend on earlier ones: every piece except the last says "Part of #N" (where N is the issue); only the final piece uses a closing keyword; merge in order; merging a base PR requires a delta check (re-review of the change) of its dependents; no force-push over reviewed commits; review fixes go on the same branch (rule 7 already says same branch).

## The Code Reviewer

11. The Reviewer starts when the pull request is marked ready, in parallel with QA where QA is required, on a Cloud Agent launched per SOP-005 on the family SOP-001 rule 5 selects. The review says which model it used. The Reviewer acts on GitHub as `shpdev-reviewer`, per SOP-003.
12. The Reviewer confirms the classification looks right and adds a tier or a flag if it doesn't. Before starting the review, check that the PR body's test counts, file list, and Model section match the head commit. Then read the whole diff, the linked requirements, and the TDD section if there is one, and check: correctness against the acceptance criteria, security (server-side authorization, input handling, secrets, personal data), reliability (failure modes, timeouts, migrations), maintainability (matches the conventions in `AGENTS.md` and the rules), that the tests would fail if the change were wrong, and that tests using dates pin/freeze the clock.
13. Every finding is labeled at the start of its comment: **Blocking**, **Should fix**, or **Nit**. Blocking means the pull request doesn't merge until it's fixed. Should fix means it's fixed in this pull request unless the author says why not and the Reviewer agrees. Nit is the author's call.
14. Bugbot, where enabled, is the first pass. The Reviewer reads its comments, keeps the ones that hold, and marks the rest resolved with a one-line reason. Bugbot never replaces the Reviewer's own review.
15. When the issue has a `risk:security` or `risk:privacy` flag, the Reviewer requests the Architect's security review on the pull request and doesn't give final approval without it. The same holds for the Cloud Engineer's review on `risk:operations`. This is a written control (C6). The CTO posts the specialist's review on the pull request, and the Reviewer checks it's there.
16. Final approval is given once, as a GitHub review with the state Approve, naming the exact commit: `Approved for <sha>`. It's given only when CI is green on that commit, every Blocking finding is fixed, QA's verdict is Pass on that commit where a verdict is required, and every specialist review the flags require is recorded. The Reviewer re-reads the head SHA from GitHub right before approving. A new commit after approval needs a new approval. Once C3 is in place, GitHub dismisses the old approval by itself. Until then, the Reviewer checks the head commit against the approved one before the merge.
17. After approving the exact SHA on GitHub, the Reviewer sends the CTO "OK to merge PR #N at <sha>" in the same session. Blocking verdicts (review, QA, specialist) are posted on GitHub in the same session, naming the SHA, not only in chat.
18. The Reviewer never pushes to the branch it reviews, never owns or requests commits on a pull request it checks, and never reviews a pull request whose `Model` section is empty.

## The QA Engineer

19. Where the table in Scope requires a verdict, QA starts when the pull request is marked ready, in parallel with the Reviewer, on a Cloud Agent launched per SOP-005 on the family SOP-001 rule 6 selects. The verdict says which model it used.
20. QA writes a short test plan from the acceptance criteria and the risks the issue and the TDD name, and runs it against the change in the test environment. For Large work, the test plan is written before build starts and linked on the issue. Risk decides depth: an authentication change gets more than a copy change.
21. The verdict is one comment on the pull request in this format. The CTO posts it on QA's behalf, verbatim, per SOP-003. It names the commit, and the Reviewer checks that it matches the head commit before approving (C5, a written control).

```markdown
## QA verdict: Pass | Fail | Blocked
Commit: <sha>
Model: <model>

| Criterion or risk | Check | Result | Evidence |
|---|---|---|---|

Defects: <links to issues filed>, or None
Blocked by: <what QA couldn't verify and why>, if Blocked
```

22. Pass means every criterion checked passed on that commit. Fail means at least one didn't, and each failure has a defect issue with steps to reproduce, linked in the verdict. Blocked means QA couldn't run a check, for a reason outside the pull request, and names it.
23. After the author pushes a fix, QA re-verifies the failed checks and anything the fix touched, and posts a new verdict for the new commit. The earlier verdict stays.
24. QA never marks its own defects fixed. The author fixes, QA re-verifies. QA never owns or requests commits on a pull request it verifies.

## The merge

25. The founder or the CTO merges, on the owner's request, after the Reviewer's final approval on the current commit and the Reviewer's "OK to merge PR #N at <sha>", using a squash merge. The merger re-reads the head SHA from GitHub right before merging. The branch is deleted after the merge.
26. The owner checks the definition of done. When it's met, the CTO closes the issue for the owner, with one comment that links the pull requests and the QA verdict. That's the completion record.
27. The Reviewer's approval is a GitHub review from `shpdev-reviewer` in the format of rule 16. The CTO never counts as the reviewer on a pull request it opened, and the founder never counts as the reviewer on one pushed through the founder's connected account.

## Acceptance by the document owners

28. For Standard and Large issues that implement a requirement, a design, or a technical design, the PM, Designer, or Architect records acceptance on the issue, per the approval gates. Acceptance is a comment with the word Accepted, the role, and the date. It can happen in the test environment with the feature flag on, before or after the merge. It doesn't block the merge, but the issue isn't done without it.

## Unavailable reviewers

29. If the Reviewer, QA, or a required specialist hasn't started within the target in SOP-008, the owner tells the Scrum Master. The Scrum Master asks the CTO or the founder to start another session of the same role: for the Reviewer, another Code Reviewer session acting as `shpdev-reviewer`, and for QA or a specialist, another session of that role, on a model family SOP-001 still allows. The substitution is recorded on the pull request. If no session can be started, the pull request waits in Blocked. A check is never skipped, and no author ever stands in for a reviewer.

## Escalation

- A disagreement between the author and the Reviewer over a Blocking finding goes to the Architect after two exchanges, then to the CTO.
- A Blocked verdict that can't be cleared within two working days goes to the Scrum Master, who decides with the PM whether the issue waits, is split, or is dropped.
- A finding that reveals a security or privacy problem in code already on `main` is raised immediately per SOP-009, not held for the pull request.

## Change history

| Date | Change | Approved |
|---|---|---|
| 2026-09-24 | First draft | CTO and founder, 2026-09-26 |
| 2026-09-24 | Rule 8 names the Reviewer's GitHub account, `shpdev-reviewer` (factual). Rule 23: the Reviewer's approval is a GitHub review instead of a comment, now that it has its own identity (process change) | CTO and founder, 2026-09-26 |
| 2026-09-24 | Identity model decided (#6): rule 2, the `Role` line always names the requesting role because the CTO opens every Cloud Agent pull request; rules 21 and 23, the founder or the CTO merges after the Reviewer approves, and the CTO never counts as the reviewer on a pull request it opened | CTO and founder, 2026-09-24 |
| 2026-09-24 | SM-016: what each pull request needs follows the tier and risk flags, QA is required for Standard, Large, and security- or privacy-flagged work, unfinished work names its feature flag, the definition of an author includes the identity that opened the pull request and every identity that pushed to it, the Reviewer and QA never own or request commits on what they check, C5 and C6 are written controls the Reviewer checks, and a substitute Reviewer is another Code Reviewer session as `shpdev-reviewer` | CTO and founder, 2026-09-26 |
| 2026-09-26 | SM-018: small PRs (~400-line soft cap or split plan before build); stacked PRs (Part of #N, merge order, delta check, no force-push over reviewed commits, same branch for fixes); exact-SHA re-reading from GitHub by Reviewer before approving and merger before merging; squash merges only, replacing rule 25's merge commit; explicit "OK to merge PR #N at <sha>" from Reviewer after GitHub Approve; blocking verdicts posted on GitHub same session naming SHA; PR body and Model section must match head before review requested, Reviewer checks this; clock-independent tests (Reviewer checks dated tests pin clock); never claim untested platforms passed | CTO and founder, 2026-09-26 |
