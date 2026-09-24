# SOP-007: Pull request, review, and QA

SM-003 as steps: what the author, the Code Reviewer, and QA each do on a pull request.

| | |
|---|---|
| Owner | Code Reviewer, with the QA Engineer |
| Status | See the [SOP index](README.md). |
| Applies to | Every role that opens, reviews, verifies, or merges a pull request |
| Related | [Root README](../README.md) step 8 and SM-003, [SOP-001](SOP-001-token-efficiency.md) rules 4 to 6, [SOP-005](SOP-005-cloud-agent-launch.md), the [pull request template](../.github/pull_request_template.md), [Bugbot](https://cursor.com/docs/bugbot) |

## Purpose

The root README says the Code Reviewer and QA work on each pull request in parallel, that QA publishes a Pass, Fail, or Blocked verdict with evidence, and that the Reviewer gives final merge approval for an exact commit once QA passes, CI is green, and blocking findings are fixed. This SOP turns that into a sequence each role can follow, with the formats their outputs take.

## Scope

Every pull request in a product repository, code or documentation. Documentation pull requests follow the author and Reviewer steps and skip QA unless the document owner asks for verification.

## The author

1. A pull request covers one issue and one story at most. A change that needs two stories is two pull requests.
2. The author fills every section of the pull request template. The `Model` section lists every model that produced a commit, per SOP-001 rule 4, and a `Role` line names the launching role until Bot identities exist, per SOP-003.
3. The pull request title starts with the requirement IDs it implements, for example `REQ-AUTH-003: password reset flow`.
4. Test evidence is the author's: what was run, where the output is, and what it shows. "Tests pass" without a link isn't evidence.
5. The pull request opens as a draft until "Done when" from the launch is met, then is marked ready. Marking it ready is the signal to the Reviewer and QA. The author posts the link on the issue and moves the issue to In review.
6. The author fixes blocking findings and QA failures on the same branch, replies on each thread with the commit that addresses it, and re-requests review. The author resolves a thread only after the Reviewer or QA confirms.
7. The author never approves, dismisses a review, or merges before the Reviewer's final approval.

## The Code Reviewer

8. The Reviewer starts when the pull request is marked ready, in parallel with QA, on a Cloud Agent launched per SOP-005 on the family SOP-001 rule 5 selects. The review says which model it used. The Reviewer acts on GitHub as `shpdev-reviewer`, per SOP-003.
9. The Reviewer reads the whole diff, the linked requirements, and the TDD section, and checks: correctness against the acceptance criteria, security (server-side authorization, input handling, secrets, personal data), reliability (failure modes, timeouts, migrations), maintainability (matches the conventions in `AGENTS.md` and the rules), and that the tests would fail if the change were wrong.
10. Every finding is labeled at the start of its comment: **Blocking**, **Should fix**, or **Nit**. Blocking means the pull request doesn't merge until it's fixed. Should fix means it's fixed in this pull request unless the author says why not and the Reviewer agrees. Nit is the author's call.
11. Bugbot, where enabled, is the first pass. The Reviewer reads its comments, keeps the ones that hold, and marks the rest resolved with a one-line reason. Bugbot never replaces the Reviewer's own review.
12. When a pull request touches authentication, authorization, personal data, or secrets, the Reviewer requests the Architect's security check on the pull request and doesn't give final approval without it.
13. Final approval is given once, as a GitHub review with the state Approve, naming the exact commit: `Approved for <sha>`. It's given only when QA's verdict is Pass on that commit, CI is green on that commit, every Blocking finding is fixed, and the security check, where required, is recorded. A new commit after approval needs a new approval.
14. The Reviewer never pushes to the branch it reviews, and never reviews a pull request whose `Model` section is empty.

## The QA Engineer

15. QA starts when the pull request is marked ready, in parallel with the Reviewer, on a Cloud Agent launched per SOP-005 on the family SOP-001 rule 6 selects. The verdict says which model it used.
16. QA writes a short test plan from the acceptance criteria and the risks the TDD names, and runs it against the change in the test environment. Risk decides depth: an authentication change gets more than a copy change.
17. The verdict is posted as one comment on the pull request in this format:

```markdown
## QA verdict: Pass | Fail | Blocked
Commit: <sha>
Model: <model>

| Criterion or risk | Check | Result | Evidence |
|---|---|---|---|

Defects: <links to issues filed>, or None
Blocked by: <what QA couldn't verify and why>, if Blocked
```

18. Pass means every criterion checked passed on that commit. Fail means at least one didn't, and each failure has a defect issue with steps to reproduce, linked in the verdict. Blocked means QA couldn't run a check, for a reason outside the pull request, and names it.
19. After the author pushes a fix, QA re-verifies the failed checks and anything the fix touched, and posts a new verdict for the new commit. The earlier verdict stays.
20. QA never marks its own defects fixed. The author fixes, QA re-verifies.

## The merge

21. The implementing engineer merges, after the Reviewer's final approval on the current commit, using a merge commit so the reviewed commits are kept. The branch is deleted after the merge.
22. The engineer moves the issue to Verifying, links the merge on the issue, and the Scrum Master checks the definition of done, per the root README and SOP-008.
23. The Reviewer's approval is a GitHub review from `shpdev-reviewer` in the format of rule 13. Until the engineers have their own identities, the founder or the CTO merges on the engineer's request.

## Acceptance by the document owners

24. Where the change implements a requirement, design, or design decision, the PM, Designer, or Architect records acceptance on the issue after the merge, per the approval gates. Acceptance is a comment with the word Accepted, the role, and the date. It doesn't block the merge, but the issue isn't done without it.

## Escalation

- A disagreement between the author and the Reviewer over a Blocking finding goes to the Architect after two exchanges, then to the CTO.
- A Blocked verdict that can't be cleared within the sprint goes to the Scrum Master, who decides with the PM whether the story carries over.
- A finding that reveals a security or privacy problem in code already on `main` is raised immediately per SOP-009, not held for the pull request.

## Change history

| Date | Change | Approved |
|---|---|---|
| 2026-09-24 | First draft | Pending CTO and founder |
| 2026-09-24 | Rule 8 names the Reviewer's GitHub account, `shpdev-reviewer` (factual). Rule 23: the Reviewer's approval is a GitHub review instead of a comment, now that it has its own identity (process change) | Pending CTO and founder |
