# Platform setup

One-time configuration the process depends on. The founder does it, once per Grok Bot workspace and once per product repository, and records each product repository's items in its `/docs/setup.md` with the date and the evidence. None of it costs tokens. A control counts as enforced only once its setting is active and its negative test has passed once.

## Identities

| Identity | Who | Access |
|---|---|---|
| `sheepnir` | The founder's personal GitHub account | Never used by a Bot. The account connected to Cursor, so Cloud Agents push branches through it. |
| `shpdev-cto` | The CTO Bot | Admin on this repository, maintain on product repositories. Opens every pull request, posts for every other role, merges. |
| `shpdev-reviewer` | The Code Reviewer Bot | Write on each repository, so its approvals count. No merge. Login in the 1Password item "GitHub - ShpDev - Code Reviewer". |

No other Bot has a GitHub identity or cloud access. The founder creates every account, credential, and external service, and Bots never do. Credentials live in 1Password and, for Cloud Agents, in Cursor's secret store. Nothing in a profile, skill, routine, chat, memory, repository, or file on the shared computer.

## Grok Bot workspace

1. **Two standing Bots.** The CTO's saved profile is [`roles/cto.md`](roles/cto.md) and the Code Reviewer's is [`roles/code-reviewer.md`](roles/code-reviewer.md). Each points at this repository for templates rather than copying them. Hide the former Product Manager, Designer, Architect, Scrum Master, Cloud Engineer, Frontend Engineer, and Backend Engineer Bots after removing their routines, because a hidden Bot's routines keep running.
2. **No Bots for the Engineer or QA.** The CTO launches each as a Cursor Cloud Agent with the launch packet in its card, pointing at [`roles/engineer.md`](roles/engineer.md) or [`roles/qa.md`](roles/qa.md).
3. **Auto Review rules.** Ask First: merge to `main`, create a release, change infrastructure, create any account, credential, or external service, enable on-demand spending, send anything outside GitHub. Always Allow: read the repository, run tests in the sandbox, post comments and reviews on the product repository. No broad "allow everything" rules. Local execution: ask every time.
4. **Secrets.** The GitHub logins and every other credential go in the Vault or 1Password, so an injected instruction can't read a secret back. The founder enters a secret directly into the store, never into chat. Any secret seen in chat is treated as exposed and rotated.
5. **Spending.** Cursor's on-demand monthly limit stays disabled (#4, 2026-09-24). Give the CTO the read access to Cursor usage that the Scrum Master had (#5), since the CTO now reads the budget.
6. **Routines, each "quiet when nothing changed" with a fallback for missing data.** Pull request opened or synchronized: the CTO asks the Code Reviewer to run, and launches QA on `tier:full`. CI completed: nothing, unless it failed, then relaunch the Engineer. Budget trip-wire: the CTO reports when most of the budget is spent and under half the points are done. Remove the former daily flow check, weekly pacing check, and monthly measures routine.
7. **Skills, taught once so they run as scripts.** Open a pull request from the template, run the suite and report, post a verdict, compile the sprint summary from milestone data. Each skill's text is also kept in the product repository under `/docs/skills/`.

## Product repository

Copy `.github/` from this repository, replace `sheepnir/GrokBot_DevFlow` in `.github/ISSUE_TEMPLATE/config.yml`, create the labels from `.github/labels.yml`, and connect the repository to Cursor with Bugbot on and Autofix off. Then:

| # | Control | Setting | Done when |
|---|---|---|---|
| C1 | PR-only `main` | Ruleset on the default branch: require a pull request, block force pushes and deletions, empty bypass list | A direct push and a force push are both blocked |
| C2 | Independent approval | Same ruleset: 1 required approval, "Require approval of the most recent reviewable push" on, code owner review off. `shpdev-reviewer` is a collaborator with Write. `CODEOWNERS` is `* @shpdev-reviewer` so every pull request requests it. Pull request creation limited to the founder and `shpdev-cto`. | A merge with no review, and one with only an author's review, are both blocked |
| C3 | Stale approvals | Same ruleset: dismiss stale approvals on new commits, require conversation resolution | Approving and then pushing dismisses the approval |
| C4 | CI | A GitHub Actions check named `ci`: lint, tests, build, with an artifact labeled by SHA. The ruleset requires it on a branch up to date with `main`. Add a job that reruns tests with the clock shifted a day and across 00:00 UTC where the stack supports it. | A failing test blocks the merge |
| C5 | Verdict on the commit | Written. The CTO merges a full-tier pull request only when QA's verdict names the head SHA. | |
| C6 | Security section | Written. The Code Reviewer approves a `needs:security-check` pull request only with the section in its review. Record the sensitive paths in `/docs/setup.md`: auth and authorization code, migrations, infrastructure, `.github/workflows/`, production flag settings. | |
| C7 | Production deployment | `test` and `production` environments with their own credentials and cloud roles assumed through OIDC. `production`: `shpdev-cto` and the founder as required reviewers, "Prevent self-review" on, deployments from `main` only. A `production-rollback` environment with the same reviewers and self-review allowed, whose only workflow restores the recorded previous known-good artifact and configuration. | A deployment waits for the other person's approval, and a rollback runs with one |
| C8 | Feature flags | A flag mechanism recorded in `/docs/decisions.md`. Flags default off in production, readable per environment, and the production settings are a `CODEOWNERS` path. | A flag turns on in test and stays off in production without a deployment |
| C9 | Secrets | Secret scanning with push protection. Secrets in the secrets manager and Cursor's secret store. Before release day, dry-run that the process that needs a secret can see it. | A push with the provider's test pattern is blocked |

Also: `AGENTS.md` at the root with how to build, test, and run, and the conventions; `.cursor/rules/*.mdc` scoped by globs; `.cursor/environment.json` so no agent spends its run installing; `.cursor/BUGBOT.md` with the Reviewer's guidance and Incremental Review on; monitoring and a cost alert that reaches the founder; the deployment and rollback procedure in `/docs/operations.md`; the folders `/docs/briefs`, `/docs/sprints`, `/docs/skills`, and the files `/docs/decisions.md` and `/docs/setup.md`; one milestone per sprint and no sprint Projects. Private deployment and credential details go in a private ops repository per product, never a public one.

## Releases

A release is `main` at one commit, and everything merged by then ships in it. Unfinished behavior is behind a flag that is off in production, and turning one on is a release. Production is deployed only by the pipeline, from the artifact CI built for that commit.

| Class | Definition | Starts | Approves |
|---|---|---|---|
| Routine | Everything it turns on is done, and it changes no cost, commitment, or risk posture | Founder | CTO |
| High-impact | The first production launch, and every release after it until the founder says otherwise; or anything that adds ongoing cost or an external commitment, or changes how personal data, authentication, or authorization work | CTO | Founder |

The GitHub Release is the record: the commit, the CI run, the flags turned on, the issues shipped, the artifact digest and configuration revision, the previous known-good target, and the outcome. Notes are generated from pull request titles. Merged, deployed, and verified are reported as separate states, and a deployment is complete only with smoke-test results, a recorded rollback target, and the founder's device check. A flag is removed within 30 days of its story closing, by a task.

Rollback restores the recorded previous known-good artifact and configuration, never the latest tag, and only when it is compatible with the current data and schema. If the target is missing or compatibility is uncertain, contain and escalate to the founder instead. A rollback that fails is an incident.

## Incidents

An urgent risk is an exposed or suspected credential or personal data, unauthorized access, an outage or data loss, an unapproved change heading to production, or a legal or safety problem with something released. Whoever sees it stops, tells the founder at once by direct message, and opens an `incident` issue with what was seen, where, when, and what was done, and no secret, personal data, or exploit detail. The CTO may turn a flag off in production, roll back when compatible, or stop a running agent, and reports it within 15 minutes. Rotation, revocation, account changes, and deletion are the founder's. Containment comes before diagnosis. Work in the affected area stops until the founder says otherwise. The CTO records a public-safe note in `/docs/decisions.md` when the incident closes.
