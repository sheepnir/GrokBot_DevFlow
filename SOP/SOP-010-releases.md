# SOP-010: Releases

Routine and high-impact releases: how unfinished work is kept out, who authorizes, and what's recorded.

| | |
|---|---|
| Owner | CTO, with the Cloud Engineer |
| Status | See the [SOP index](README.md). |
| Applies to | The CTO, the Cloud Engineer, the Scrum Master, the PM, the Architect, and the founder |
| Related | [Root README](../README.md) "Controls" and the approval gates, [SOP-004](SOP-004-new-product-repository.md) items 16 to 22, [SOP-009](SOP-009-escalation-and-urgent-risks.md), [SOP-011](SOP-011-autonomous-execution.md) permissions |

## Purpose

The root README gives the CTO authority over routine releases, and the founder authority over the first production launch and any release that adds ongoing cost, external commitments, or material security or privacy risk. This SOP says how a release is classified, how unfinished work that has already merged is kept out of it, what's checked, and what's recorded.

## Scope

Every deployment to production, and every change to a feature flag in production. Deployments to test environments are routine work under SOP-011 and need no release record.

## Keeping unfinished work out

The team uses **feature flags**. It doesn't keep a list of excluded issues.

1. `main` is always releasable. A release is `main` at one commit, and everything merged by then ships in it.
2. Work whose issue isn't Done merges only behind a feature flag that's off in production. The flag is named in the pull request template and on the issue.
3. A change that can't sit behind a flag merges only when its issue can be Done. That includes a migration that changes or removes existing data, an infrastructure change, and a dependency upgrade. Its acceptance happens in the test environment before the merge. Additive migrations are written so the old code keeps working, and may merge ahead.
4. Turning a flag on in production is a release decision, recorded in the release like a code change. Turning one off to contain harm is an emergency action under SOP-011.
5. A flag is removed within 30 days of its issue being Done, by a Small follow-up issue that the original owner opens when closing.

Until the flag mechanism (SOP-004 item 19) exists, rule 2 can't be met. Unfinished work stays on its branch and doesn't merge.

## Classification

| Class | Definition | Authorizes |
|---|---|---|
| Routine | Everything it turns on is Done, it changes no cost, commitment, or risk posture, and it isn't the first production launch | CTO |
| High-impact | The first production launch, or a release that turns on anything with a `risk:external` flag, adds ongoing cost, changes how personal data is handled, or changes authentication or authorization in a way the Architect calls material | Founder, after the CTO recommends it |

The Scrum Master proposes the class in the release draft. The CTO confirms it. If in doubt, it's high-impact.

## The release record

The release record is the GitHub release for the tag. It isn't a separate file, and it links to evidence instead of restating it. The Scrum Master drafts it and the Cloud Engineer completes the deployment lines.

```markdown
Class: Routine | High-impact
Commit: <sha>, CI: <link to the green run on this commit>
Flags turned on in production: <flag, issue link>, or none
Issues shipped complete: <links>

Needs attention
- Risk-flagged issues: <links>, each with its security review, or none
- Migrations: <none | reversible | not reversible, with the rollback plan link>
- Cost delta: <none | amount per month, with the founder's approval link>
- External commitments: <none | listed, with the founder's approval link>

Authorization: <link to the production environment approval>, or, until C7 exists, CTO <date> and founder <date> if high-impact
Deployed: <UTC time>, by the pipeline run <link>
Verified after deploy: <what was checked, and the result>
Outcome: Held | Rolled back, with the reason and the follow-up issues
```

Definition of done evidence, CI results, and acceptance are already on the issues and pull requests. The record links to them. It doesn't re-check them line by line.

## Rules

6. A release is authorized before it deploys. Once C7 exists, the authorization is the approval on the `production` environment, by the CTO, or the founder for a high-impact release, and the pipeline can't deploy without it. Until then, it's written in the release draft with the date, and the Cloud Engineer doesn't deploy without it. Silence isn't authorization, and a release isn't authorized by being scheduled.
7. The Cloud Engineer deploys through the pipeline from SOP-004, never by hand, and never from a machine other than the pipeline. The pipeline deploys the artifact CI built for the release commit. A deployment the pipeline can't do is a change to the pipeline first.
8. After deployment, the Cloud Engineer verifies the changed behavior in production and records the result. QA verifies user-facing changes where a test in production is safe.
9. A release is rolled back, without waiting for a diagnosis, when any of these happens: an alert fires on the changed behavior, a user-facing error rate rises above the threshold in `/docs/architect/operations.md`, data is found to be wrong, or a security or privacy problem is found. If a flag caused it, turning the flag off comes first. The rollback is an emergency action under SOP-011, and it's recorded on the release. An `incident` issue is opened per SOP-009 if the cause is a risk.
10. Release notes for user-facing changes are written by the PM in `/docs/product/releases.md`, from the issues, before the release is authorized.
11. From the first production launch until the founder says otherwise, every release is high-impact.

## Escalation

- A disagreement over the class goes to the CTO. If the CTO calls it routine and the Scrum Master or the Architect still thinks it's high-impact, the founder is told before it ships. Telling the founder isn't asking for approval, but it gives them the chance to require it.
- A rollback that fails is an incident per SOP-009.

## Change history

| Date | Change | Approved |
|---|---|---|
| 2026-09-24 | First draft | Pending CTO and founder |
| 2026-09-24 | SM-016: feature flags keep unfinished work out of a release instead of excluding issues, the GitHub release is the release record and links evidence instead of re-checking it, and authorization moves to the `production` environment approval once C7 exists | Pending CTO and founder |
