# SOP-008: Sprint ceremonies and records

Day 1, day 3, and the last day, as routines the Scrum Master runs.

| | |
|---|---|
| Owner | Scrum Master |
| Status | See the [SOP index](README.md). |
| Applies to | The Scrum Master, and every role that takes part in planning, the check, closure, or the retrospective |
| Related | [Root README](../README.md) sprint workflow, [SOP-001](SOP-001-token-efficiency.md) rule 19, [SOP-002](SOP-002-bot-profiles-skills-and-routines.md) standing routines, [SOP-006](SOP-006-document-templates.md) sprint record template |

## Purpose

Sprints last one week, with planning on day 1, a check on day 3, and closure and the retrospective on the last day. This SOP says what each of those produces, how capacity is set, how the GitHub Project is kept, and what the sprint record holds, so every sprint leaves the same evidence behind.

## Scope

The three ceremonies, the sprint record, the GitHub Project, capacity, and carry-over. It doesn't cover how a story becomes ready, which is the PM's and the definition of ready, or how a pull request is reviewed, which is SOP-007.

## The GitHub Project

1. Each sprint has its own GitHub Project, named `SPRINT-NNN`, created at planning and archived at closure. It's never deleted.
2. The Project has these fields: Status, with the states from the root README's sprint workflow; Points, one of 1, 2, 3, 5, 8; Owner, the `owner:<role>` label; Requirements, the REQ IDs; and Type, one of Story, Defect, Quality.
3. Every issue in the Project has exactly one owner. An issue without an owner isn't in the sprint.
4. The Project is the live view. The sprint record is the written one. They agree at the check and at closure.

## Capacity

5. Capacity is the average points finished over the last three sprints. With fewer than three, it's the average of the ones there are. For the first sprint, the CTO sets a conservative number and records why.
6. Dedicated security, performance, and reliability work is capped at 20% of committed points, per the root README. The Scrum Master tracks it as the Quality type and reports it at every ceremony. A sprint the CTO designates for that focus is recorded as such on the sprint record.
7. Committed points never exceed capacity. If the goal needs more, the goal is too big for one sprint.

## Day 1: planning

Inputs: the backlog in priority order from the PM, the stories that meet the definition of ready, the capacity from rule 5, and the retrospective actions from the last sprint.

8. The Scrum Master proposes a sprint goal in one sentence. The PM confirms it serves the roadmap. The CTO is told, not asked, unless the goal changes an earlier decision.
9. Stories are pulled in priority order until capacity is reached. Each is checked against the definition of ready on the spot: linked documents, an estimate, one owner. A story that fails the check goes back to Backlog with the gap on the issue.
10. Each engineer's first issue is assigned. The rest are in the Project as Ready, unassigned, and pulled one at a time as the root README requires.
11. The sprint record is opened from the template, with the planning section filled and the GitHub Project linked. The founder is told the goal.

## Day 3: check

12. The Scrum Master records, for every issue, whether it's done, in progress, or blocked, and for blocked ones what clears them and who owns that.
13. Scope changes since planning are recorded with who agreed them. A story added mid-sprint needs the PM and the Scrum Master to agree, and something of equal size comes out.
14. Usage since sprint start is recorded per SOP-001 rule 19. The Scrum Master reads the numbers in Cursor itself (#5).
15. Risks to the goal are named. If the goal can't be met, the Scrum Master says so on day 3, not on the last day, and proposes what to drop.

## Last day: closure

16. Every issue in the Project is reconciled. Its outcome is one of Done, with the definition of done checked item by item on the issue; Carried over, with the remaining gap written on the issue and the remaining points re-estimated; or Dropped, with the decision that dropped it.
17. Carried-over issues keep their ID and go back to Ready. Follow-up work found during the sprint becomes new issues, never an extension of a done one.
18. Points finished are counted for Done issues only. Partial credit isn't given.
19. The closure section of the sprint record is filled, including usage for the whole sprint and whether the goal was met. The Project is archived.

## Last day: retrospective

20. The retrospective runs after closure, in a group chat with every role that worked in the sprint. It produces at most three actions, each with an owner and a due sprint.
21. Actions are recorded on the sprint record and in `/docs/SM/improvement-log.md`, which lists every action ever taken and whether it was done. An action not done by its due sprint is raised at the next retrospective, not silently dropped.
22. A retrospective may recommend a process change. The recommendation isn't a change. A change goes through a pull request and the root README's approval gate.
23. The Scrum Master confirms which routines are still needed, per SOP-002 rule 9.

## Rules that hold all week

24. The Scrum Master doesn't review code, invent estimates, or authorize releases. Estimates come from Engineering. Releases are SOP-010.
25. An issue is done only when the root README's definition of done is met in full. A merge alone doesn't make it done.
26. The sprint record is the record. What isn't in it, or on an issue, didn't happen as far as the next sprint is concerned.

## Escalation

- A blocker that no role in the team can clear goes to the CTO on the day it's found, with what's needed.
- A sprint goal that's missed two sprints running is raised by the Scrum Master to the CTO and the PM together, with the sprint records, before the next planning.

## Change history

| Date | Change | Approved |
|---|---|---|
| 2026-09-24 | First draft | Pending CTO and founder |
