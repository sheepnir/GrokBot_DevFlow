# SOP-008: Flow, queue, and reviews

The persistent Project, the queue, the work-in-progress limits, the four measures, and when a review is held.

| | |
|---|---|
| Owner | Scrum Master |
| Status | See the [SOP index](README.md). |
| Applies to | The Scrum Master, the PM who orders the queue, and every owner who pulls from it |
| Related | [Root README](../README.md) "Workflow states" and "Measures", [SOP-001](SOP-001-token-efficiency.md) rule 19, [SOP-002](SOP-002-bot-profiles-skills-and-routines.md) standing routines, [SOP-006](SOP-006-document-templates.md) review record template, [SOP-011](SOP-011-autonomous-execution.md) |

## Purpose

Work flows continuously instead of in one-week sprints. This SOP keeps that flow visible and bounded. It covers one Project that never gets recreated, a queue the PM orders, limits that stop the team from starting more than it can finish, four measures that come from data the team already produces, and reviews held when there's a decision to make.

## Scope

The GitHub Project, the queue, the work-in-progress limits, milestones, the measures, and reviews. It doesn't cover how an issue is classified, which is in the root README, how work is claimed and recovered, which is SOP-011, or how a pull request is reviewed, which is SOP-007.

## The Project

1. Each product repository has one GitHub Project, named for the product, created once by SOP-004. It's never archived on a schedule or recreated.
2. The Project has these fields: Status, with the states from the root README; Tier; Risk flags; Owner, from the `owner:<role>` label; Requirements, the REQ IDs if any; Type, one of Story, Defect, or Quality; and Milestone, where there is one.
3. The Project is the live view. The issues are the record. Nothing is copied from one into the other by hand. The CTO keeps the Project's fields in step as it posts for owners. The Scrum Master reads the Project, and the issue and pull request timelines, per SOP-011 rule 3.

## The queue

4. The PM keeps Ready in priority order. A defect that breaks something in production goes to the top.
5. An owner pulls the top Ready issue it can do, through the CTO, per SOP-011. If it skips one, it gives the CTO one line on why, and the CTO posts it on the skipped issue.
6. An issue enters Ready only when it meets the root README's definition of ready. The PM moves it there.

## Work-in-progress limits

7. The limits and response targets are settings. The Scrum Master proposes changes from the measures, and the CTO sets them in this table with the date.

| Setting | Value |
|---|---|
| Issues In progress per owner | 1 |
| Pull requests ready and waiting for the Code Reviewer | 4 |
| Pull requests ready and waiting for QA | 4 |
| Large milestones in flight | 1, or 2 with the CTO's agreement |
| Dedicated quality work In progress | 1 issue at a time, unless the CTO designates a period of focus |
| Start of a review or a QA run after the pull request is marked ready | Within one working day |
| Blocked before the Scrum Master escalates to the CTO | Two working days |

8. When a review or QA limit is reached, no issue moves to In progress until it drops below the limit. Owners use the time to finish work: they answer review threads, fix their own findings, and clear blocks. Nobody approves or verifies their own work to drain the queue.
9. An owner whose issue is Blocked may claim one more issue. It returns to the blocked one as soon as the block clears.

## Milestones

10. Large work is planned as a GitHub milestone. Its description holds the goal in one sentence, the BRD and TDD links, a target date, and the budget the CTO set when confirming the tier. Other work uses a milestone only when several issues must ship together.
11. A milestone that will miss its target date is flagged by its owner as soon as that's known, with what could be dropped. It isn't flagged on the last day.
12. When a milestone closes, a review is held if one of the triggers in rule 17 applies. Otherwise its closing comment links the issues and the release, and that's the record.

## Measures

13. The Scrum Master tracks four measures, from data the team already produces:

| Measure | Definition | Source |
|---|---|---|
| Delivery time | Median working days from the claim to the issue closing, per tier | The claim comment and the issue's close date |
| Waiting time | Time with the `blocked` label, plus time a ready pull request waits for its first review and for approval, per tier | The issue timeline's label events, and the pull request's ready, review, and approval times |
| Escaped defects | Defects opened after the issue that caused them was Done, with the `escaped` label and a link to that issue | Defect issues |
| Agent usage | Cursor usage by pool for the month, and per issue where the dashboard shows it | The Cursor dashboard, per SOP-001 rule 19 |

14. On the first working day of each month, the Scrum Master's measures routine appends one row to `/docs/SM/measures.md`: the four numbers for the month and one sentence on what changed. The same routine runs the spending check from SOP-001 rule 19: on-demand spending still disabled, on-demand spend $0, and the share of the included allocation used, reported to the CTO and the founder. The weekly pacing check (SOP-002) warns them sooner if usage runs ahead. There's no other report.
15. A measure that nobody has used for a decision in three months is proposed for removal.
16. Measures describe the flow, not people. They aren't used to rank owners.

## Reviews

17. A review is held when there's a decision to make or a recurring problem to address. Any of these triggers one:
    - a milestone closes and its delivery time or waiting time missed the target
    - an escaped defect breaks something in production, or two defects escape in one month
    - the same blocker, review finding, or agent failure shows up on two issues
    - a measure gets worse two months running
    - anyone asks for one to settle a decision that needs several roles

    An incident gets its post-incident review under SOP-009 instead, not a second one here.
18. The Scrum Master runs the review in a group chat with only the roles involved, and records it from the review record template in `/docs/SM/reviews/`. It produces at most three actions, each with an owner and a due date.
19. Actions are also listed in `/docs/SM/improvement-log.md`, with whether they were done. An action not done by its due date is raised at the next monthly measures routine, not silently dropped.
20. A review may recommend a process change. The recommendation isn't a change. A change goes through a pull request and the root README's approval gate.

## Rules that hold all the time

21. The Scrum Master doesn't review code or authorize releases. Its job is the flow: limits, stalls, measures, and reviews.
22. An issue is done only when the root README's definition of done is met in full. A merge alone doesn't make it done.
23. The issues and pull requests are the record. A review record or a measures row links to them. It doesn't restate them.

## Escalation

- A block that no role can clear goes to the CTO once the limit in rule 7 is reached, with what's needed.
- A milestone that misses its target date twice is raised by the Scrum Master to the CTO and the PM together, with the measures.

## Change history

| Date | Change | Approved |
|---|---|---|
| 2026-09-24 | First draft, as sprint ceremonies and records | Pending CTO and founder |
| 2026-09-24 | Replaced by flow, queue, and reviews as part of SM-016: one persistent Project, work-in-progress limits instead of sprints and story points, four measures, and reviews held on triggers | Pending CTO and founder |
| 2026-09-25 | CTO review: the monthly spending check from SM-014 moves to the measures routine on the first working day of the month, measures come from issue and pull request timelines, and owners act on the queue through the CTO | Pending CTO and founder |
