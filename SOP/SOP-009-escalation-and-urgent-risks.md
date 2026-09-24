# SOP-009: Escalation and urgent risks

How a routine disagreement reaches the CTO, and what a Bot does the moment it sees a security, privacy, or production risk.

| | |
|---|---|
| Owner | CTO |
| Status | See the [SOP index](README.md). |
| Applies to | Every Bot |
| Related | [Root README](../README.md) "Collaboration before escalation", [SOP-003](SOP-003-bot-identities-and-access.md) exposure rules |

## Purpose

The root README sets two rules: consult the relevant teammate before escalating, and escalate to the CTO after two focused exchanges don't resolve it. It also says urgent security, privacy, or production risks are raised immediately. This SOP gives both a procedure, so a Bot knows what to write, whom to involve, and what never to do on its own.

## Scope

Routine escalation of questions and disagreements, and the response to urgent risks. It doesn't cover access requests, which are SOP-003, or review disagreements, which start in SOP-007 and end here.

## Routine escalation

1. Before escalating, the Bot consults the role that owns the matter: the PM for product intent and queue order, the Architect for technical design and risk flags, the Designer for design, and the Scrum Master for ownership, dependencies, and flow.
2. A focused exchange is one message that states the issue, the evidence, the constraints, and a proposed solution, and one reply. Two of those, without agreement, is the trigger.
3. The escalation is one comment on the issue, or a new issue labeled `escalation` if there isn't one, in this format:

```markdown
## Escalation to the CTO
Issue: <link>
Roles involved: <roles>
Question: one sentence, ending in a question mark

Options considered
1. <option>: for, against
2. <option>: for, against

Remaining disagreement: what each side holds and why
Recommendation: which option, and why
Decision needed by: <date>, and what happens if it's later
```

4. Only the roles needed are involved. The Bot tags the CTO and the roles in the exchange, and no one else.
5. The CTO's decision is recorded on the issue, and in a decision record per SOP-006 if it would be expensive to reverse. It stands until new information reopens it.
6. Agreement between peers doesn't replace a required approval. An escalation isn't needed to get an approval. The approval gate is.
7. Silence isn't a decision. If the CTO hasn't answered by the date in the escalation, the Scrum Master raises it with the founder.

## Urgent risks

An urgent risk is any of: a credential or personal data exposed or suspected exposed; unauthorized access, or a change that would grant it; a production outage or data loss; a change about to go to production without its required approval; or a legal or safety problem with something already released.

8. The Bot that sees it stops what it's doing on that matter. It doesn't try to contain the risk by taking an action it isn't approved for, such as rotating a credential, deleting data, or changing production. The only exceptions are the emergency actions SOP-011 authorizes in advance for named roles: turning a feature flag off in production, rolling back to the last release through the pipeline, and stopping a running agent.
9. It tells the CTO and the founder at once, both of them, in a direct message and on a new issue labeled `incident`. The issue states what was seen, where, when, and what the Bot did and didn't do. It contains no secret, no personal data, and no exploit detail, because repositories are public.
10. The CTO runs the response. The founder does any action that needs the founder's access, per SOP-003: rotation, revocation, account changes.
11. Containment comes before diagnosis. The first actions stop the exposure or the outage. Root cause comes after.
12. The Bot that reported keeps a timeline on the issue, in UTC, of every action taken and by whom, until the CTO closes the incident.
13. Work related to the incident stops until the CTO says it can continue. Other work continues.
14. Within five working days of the incident closing, the Architect writes a post-incident review under `/docs/architect/decisions/` as a security note: what happened, why, what was done, and what changes. The changes become issues. The note contains nothing sensitive.
15. Nothing about an incident is posted outside the team's channels and the incident issue. Public disclosure, if any, is the founder's decision.

## What a Bot never does

- Takes a containment action it isn't approved for, however urgent. The emergency actions in SOP-011 are approved in advance for the roles named there.
- Waits to tell the founder and the CTO until it has a diagnosis.
- Puts a secret, personal data, or an exploit into an issue, a chat, or a document.
- Treats an incident as resolved because the symptom stopped.

## Escalation of this SOP

- If the CTO is unreachable during an incident, the Bot escalates to the founder alone and says so on the issue. The founder decides.

## Change history

| Date | Change | Approved |
|---|---|---|
| 2026-09-24 | First draft | Pending CTO and founder |
| 2026-09-24 | SM-016: the emergency actions authorized in advance by SOP-011 are named as the exceptions to rule 8, and deadlines no longer refer to sprints | Pending CTO and founder |
