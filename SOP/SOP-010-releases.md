# SOP-010: Releases

Routine and high-impact releases: the criteria, the checklist, and who authorizes.

| | |
|---|---|
| Owner | CTO, with the Cloud Engineer |
| Status | See the [SOP index](README.md). |
| Applies to | The CTO, the Cloud Engineer, the Scrum Master, the PM, and the founder |
| Related | [Root README](../README.md) step 9 and the approval gates, [SOP-004](SOP-004-new-product-repository.md) items 15 to 19, [SOP-009](SOP-009-escalation-and-urgent-risks.md) |

## Purpose

The root README gives the CTO authority over routine releases and the founder over the first production launch and any release that adds ongoing cost, external commitments, or material security or privacy risk. This SOP says how a release is classified, what's checked before it goes, who does what, and what's recorded.

## Scope

Every deployment to production. Deployments to test environments are the Cloud Engineer's routine work and need no release record.

## Classification

| Class | Definition | Authorizes |
|---|---|---|
| Routine | Every issue in it is Done, it changes no cost, commitment, or risk posture, and it isn't the first production launch | CTO |
| High-impact | The first production launch, or a release that does any of: adds ongoing cost, makes an external commitment such as a public API, a contract, or a customer-visible promise, changes how personal data is handled, or changes authentication or authorization in a way the Architect calls material | Founder, after the CTO recommends it |

The Scrum Master proposes the class on the release record. The CTO confirms it. If in doubt, it's high-impact.

## Release readiness

The Scrum Master assembles the release record from the template below and checks every line. The Cloud Engineer confirms the operational lines. Nothing is released with an unchecked line.

```markdown
# Release <version or date>

Class: Routine | High-impact
Sprint: SPRINT-NNN
Issues: <links>, all Done

## Readiness
- [ ] Every issue is Done by the definition of done, with evidence linked
- [ ] CI is green on the exact commit being released
- [ ] Every change touching authentication, authorization, personal data, or secrets has the Architect's security check recorded
- [ ] Product, design, and technical acceptance are recorded where they apply
- [ ] Migrations have been run in the test environment and are reversible, or the rollback plan says why not
- [ ] Monitoring covers the changed behavior, and alerts reach the CTO and the founder
- [ ] The rollback procedure in /docs/architect/operations.md applies to this release, or has been updated
- [ ] Documentation is updated where the change requires it
- [ ] Cost delta: none, or the amount per month and the founder's approval link
- [ ] External commitments: none, or listed with the founder's approval link

## Authorization
| Role | Decision | Date |
|---|---|---|
| CTO | | |
| Founder (high-impact only) | | |

## Deployment
Deployed by: <role>, at <UTC time>, commit <sha>
Verification after deploy: <what was checked and the result>

## Outcome
Held | Rolled back, with the reason and the follow-up issues
```

## Rules

1. Only Done issues ship. An issue that isn't Done is left out of the release, not hurried.
2. A release is authorized in writing on the release record, with the date. Silence isn't authorization, and a release isn't authorized by being scheduled.
3. The Cloud Engineer deploys through the release automation from SOP-004, never by hand, and never from a machine other than the pipeline. A deployment the pipeline can't do is a change to the pipeline first.
4. Production changes are made under the Cloud Engineer's per-change approval from SOP-003. The approval is the CTO's authorization on the release record.
5. After deployment, the Cloud Engineer verifies the changed behavior in production and records the result. QA verifies the user-facing changes where a test in production is safe.
6. A release is rolled back, without waiting for a diagnosis, when any of: an alert fires on the changed behavior, a user-facing error rate rises above the threshold in `operations.md`, data is found to be wrong, or a security or privacy problem is found. The rollback is recorded on the release record, and an `incident` issue is opened per SOP-009 if the cause is a risk.
7. Release notes for user-facing changes are written by the PM in `/docs/product/releases.md`, from the issues, before the release is authorized.
8. Release records live in `/docs/SM/releases/`, one file per release, and are linked from the sprint record.
9. Between the first production launch and the founder's say-so, every release is high-impact.

## Escalation

- A disagreement over the class goes to the CTO. If the CTO calls it routine and the Scrum Master or the Architect still thinks it's high-impact, the founder is told before it ships. Telling the founder isn't asking for approval, but it gives them the chance to require it.
- A rollback that fails is an incident per SOP-009.

## Change history

| Date | Change | Approved |
|---|---|---|
| 2026-09-24 | First draft | Pending CTO and founder |
