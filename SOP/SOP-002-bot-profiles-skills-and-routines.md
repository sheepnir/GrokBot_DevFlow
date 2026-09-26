# SOP-002: Bot profiles, skills, and routines

How each role's Bot is defined, so ten Bots are staffed one way.

| | |
|---|---|
| Owner | CTO |
| Status | See the [SOP index](README.md). |
| Applies to | Every Bot on the SHP Development team, and whoever creates or edits one |
| Related | [Root README](../README.md), [SOP-001](SOP-001-token-efficiency.md), [Grok Bot overview](https://docs.x.ai/grok-bot/overview), [Grok Bot FAQ](https://docs.x.ai/grok-bot/faq) |

## Purpose

A Bot is defined by four things: its profile, its skills, its routines, and what it's told in conversation. Each holds a different kind of information. When the wrong kind lands in the wrong place, the Bot either carries stale context into every message, which costs tokens, or forgets a rule it should always follow, which costs rework. This SOP says what goes where, gives every role the same profile shape, and sets the conventions for skills and routines.

## Scope

This SOP covers the content of profiles, skills, and routines, the templates for them, and how they're created, changed, shared, and retired. It doesn't cover which model a Bot's Cloud Agents use, which is SOP-001, or the identity a Bot acts under, which is SOP-003.

## What goes where

| Place | Holds | Never holds | Changes |
|---|---|---|---|
| Profile | Rules that are true every week: the role, what it owns, who it reports to, its surfaces, the decisions it doesn't make, and its approval boundaries | Milestone goals, current issues, anything with a date, credentials | Only through a pull request to this repository or the product repository, per the approval gates |
| Skills | A tested, repeatable procedure with inputs, steps, output, validation, and approval boundaries | Judgment calls, one-off tasks, credentials | Edited like code: proposed, tested manually, then saved |
| Routines | A skill or task assigned to one Bot on a schedule or event trigger, with where to read inputs and where to write outputs | Anything that needs a decision the routine can't make | Created by the owning role, confirmed each month by the measures routine |
| Conversation and issues | The context of current work: the issue in progress, its progress notes, blockers, decisions in flight | Standing rules, which would have to be repeated for every issue | Every day |
| Files on the shared computer | Working files a task needs, in a folder named for the role | Secrets, personal data, anything the whole team may not see | As the work requires |

Grok Bot keeps a Bot's memory, files, browser sessions, and preferences across sessions, and every Bot on the account shares one cloud computer. That's why standing rules belong in the profile and not in memory: memory is a summary the Bot writes for itself and can drift, while the profile is reviewed text.

The [versioned profiles](profiles/README.md) include all ten roles and shared rules. Install and read back the saved instructions using the [activation checklist](rollout/activation-checklist.md); do not infer installation from a repository change.

## Profile template

Every Bot's profile uses this shape, in this order. Section headings are kept so a reader can compare two profiles side by side.

```markdown
# <Role name>

## Job
One sentence. What this Bot is for.

## Owns
The bullets from the "Owns" column of the root README's roles table, verbatim.

## Reports to
<Role>. Takes technical direction from <Role> and product intent from <Role>, where that applies.

## Surfaces
- Coordination, memory, and messages: this Bot.
- Documents, code, tests, reviews, and verification: a Cursor Cloud Agent, launched per SOP-005, on the model SOP-001 assigns to this role.

## Standing rules
Numbered. Only rules that hold every week. Each one is something the Bot can check before it acts.

## Never
Numbered. The things this Bot doesn't do, starting with the decisions that belong to other roles. Doing another role's work on an issue it owns is allowed. Making that role's decision isn't.

## Approval boundaries
What this Bot does only after an explicit approval, and from whom. Silence is never approval.

## Start rule
Wait for a message that names a task before starting any work. This profile is not a task.
```

The start rule is not optional. A profile without it can be read by the Bot as a live instruction the moment it's saved.

## Skill conventions

1. A skill is one procedure with one output. A skill that produces two kinds of output is two skills.
2. A skill is named `<role>-<verb>-<object>`, for example `sm-append-measures-row` or `qa-write-verdict`.
3. A skill isn't saved until the procedure has been run by hand once and produced the right output. The demonstration can be recorded, and Grok Bot can draft the skill from it, but the draft is edited before it's saved.
4. Every skill states its inputs, its steps, its output and where it goes, how to check the output is right, and the point at which it stops for approval, if any.
5. Skills are shared across every Bot on the account. A skill says which role maintains it. Another role may run it for an issue it owns. If the skill ends in a decision, such as an acceptance or a verdict, the decision still belongs to the maintaining role.
6. The text of every saved skill is also kept in the product repository under `/docs/SM/skills/<skill-name>.md`, so it's reviewable in a pull request and survives the Bot. The saved skill and the file say the same thing. When they differ, the file wins and the skill is updated.

## Routine conventions

7. A routine names its Bot, its trigger (a schedule with a time zone, or an event), where it reads inputs, where it writes outputs, its approval boundary, and what it does when an input is missing.
8. Each Bot keeps its routines to the ones it needs. Grok Bot allows up to 50 per Bot and keeps the 20 most recent runs, so a routine that matters more than that writes its own record to the repository.
9. Grok Bot may pause a routine after prolonged inactivity. The Scrum Master's monthly measures routine confirms which routines are still needed, and the owning role removes the rest.
10. A routine never sends, publishes, deletes, purchases, or changes production on its own. Those actions stop at the approval boundary.

### Standing routines

These exist from the day the first product repository is set up. Other routines are added by the owning role as needed.

| Bot | Routine | Trigger | Output |
|---|---|---|---|
| Scrum Master | Flow check | Every working day | Stalls, failed agents, red CI, unstarted reviews, and long blocks, read from GitHub and sent to each owner and the CTO in the team chat, per SOP-011 rules 3 and 14 to 16. Nothing is sent when nothing is wrong. |
| Scrum Master | Pacing check | Every week | The share of the included allocation used against the share of the period elapsed, per SOP-001 rule 19. The CTO and the founder are told only when usage is ahead of pace. |
| Scrum Master | Measures and spending | First working day of each month | One row in `/docs/SM/measures.md` with the four measures and usage, per SOP-008. The spending check from SOP-001 rule 19: on-demand still disabled, on-demand spend $0, and the allocation used, reported to the CTO and the founder. Plus the routine check from rule 9, and the access review at the start of each quarter, per SOP-003. |
| Cloud Engineer | Infrastructure check | Weekly | Costs, monitoring status, and backup status recorded in `/docs/architect/quality-backlog.md` |

## Creating, changing, sharing, and retiring a Bot

11. A new Bot is created from its profile in this SOP's template, by the CTO or the founder. The profile is committed to this repository under `SOP/profiles/<role>.md` before the Bot is created, and the Bot's saved profile matches it.
12. A change to a profile is a change to the process. It goes through a pull request and the root README's approval gate.
13. A Bot may be shared as a template with a team-only link, never a public one. A shared template carries no logins, files, or conversation history, so the receiver still needs SOP-003.
14. A Bot that's no longer needed is hidden, not deleted. Hiding keeps its files and lets it be brought back. Its routines are removed first, because a hidden Bot's routines keep running.
15. If a Bot is deleted, the founder checks the shared computer afterwards for files and logins the Bot left behind, and removes them.

## Escalation

- A Bot that finds a standing rule in its conversation, or current work context in its profile, tells the Scrum Master, who moves it to the right place.
- A Bot unsure whether running another role's skill would make that role's decision asks the maintaining role first.
- Two Bots whose skills produce conflicting outputs raise it to the Scrum Master, who involves the Architect for technical procedures and the PM for product ones.

## Change history

| Date | Change | Approved |
|---|---|---|
| 2026-09-24 | First draft | Pending CTO and founder |
| 2026-09-24 | SM-016: the profile's Never section covers other roles' decisions, not their work; skills may be run by an issue's owner; sprint routines replaced by the daily flow check and the monthly measures routine | Pending CTO and founder |
| 2026-09-25 | CTO review: the flow check reads GitHub and reports in chat, the monthly spending check from SM-014 moves to the first working day of the month, and a weekly pacing check is added | Pending CTO and founder |
| 2026-09-26 | SM-017: align runtime, coordination, recovery, and rollout instructions with the activation checklist | Pending CTO and founder |
