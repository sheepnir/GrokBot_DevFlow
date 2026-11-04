# Platform setup

One-time configuration the process depends on. The founder does it, once per Grok Bot workspace and once per product repository, and records each product repository's items in its `/docs/setup.md` with the date and the evidence. None of it costs tokens. A control counts as enforced only once its setting is active and its negative test has passed once.

## Identities

| Identity | Who | Access |
|---|---|---|
| `sheepnir` | The founder's personal GitHub account | No Bot acts as this account. Cloud Agent pushes go through its Cursor connection, which is why it counts as an author under C2. |
| `shpdev-cto` | The CTO Bot | Admin on this repository, maintain on product repositories. Opens every pull request, posts for every other role, merges. |
| `shpdev-reviewer` | The Code Reviewer Bot | Write on each repository, so its approvals count. No merge. Login in the team password manager. |
| Claude GitHub App | Anthropic's app, installed by the founder | Clones for Claude Code tasks and the review workflow, pushes their branches through the founder's GitHub connection, posts review comments. Never approves. |
| Codex | OpenAI's GitHub connection, authorized by the founder | Posts the first-pass review and pushes `@codex fix` commits to a pull request branch. Never approves. |

No other Bot has a GitHub identity or cloud access. The founder creates every account, credential, and external service, and Bots never do. Credentials live in the team password manager and, for executors, in Cursor's secret store or the Claude cloud environment's network secrets. Nothing in a profile, skill, routine, chat, memory, repository, or file on the shared computer.

## Grok Bot workspace

1. **Two standing Bots.** The CTO's saved profile is [`roles/cto.md`](roles/cto.md) and the Code Reviewer's is [`roles/code-reviewer.md`](roles/code-reviewer.md). Each points at this repository for templates rather than copying them. Hide the former Product Manager, Designer, Architect, Scrum Master, Cloud Engineer, Frontend Engineer, and Backend Engineer Bots after removing their routines, because a hidden Bot's routines keep running.
2. **No Bots for the Engineer or QA.** The CTO launches each on an executor pool with the launch packet in its card, pointing at [`roles/engineer.md`](roles/engineer.md) or [`roles/qa.md`](roles/qa.md): a Claude Code task through the `fire-task` skill, a Cursor Cloud Agent, or the `dispatch-cli` skill on the shared computer.
3. **Auto Review rules.** Ask First: merge to `main`, create a release, change infrastructure, create any account, credential, or external service, enable on-demand spending, send anything outside GitHub. Always Allow: read the repository, run tests in the sandbox, post comments and reviews on the product repository, run the `fire-task` skill. No broad "allow everything" rules. Local execution: ask every time.
4. **Secrets.** The GitHub logins and every other credential go in the Vault or the team password manager, so an injected instruction can't read a secret back. The founder enters a secret directly into the store, never into chat. Any secret seen in chat is treated as exposed and rotated.
5. **Spending.** Cursor's on-demand monthly limit stays disabled (#4, 2026-09-24). Give the CTO the read access to Cursor usage that the Scrum Master had (#5), since the CTO now reads the budget.
6. **Routines, each "quiet when nothing changed" with a fallback for missing data.** Executor report, a webhook routine on the CTO: a finished Claude Code task or CLI run posts its report as the body, and the CTO opens the pull request or posts the verdict, and nothing else. Pull request opened or synchronized: the CTO waits for the first pass, asks the Code Reviewer to run, and launches QA on `tier:full`. CI completed: nothing, unless it failed, then relaunch the Engineer. Budget trip-wire: the CTO reports when most of the budget is spent and under half the points are done. Remove the former daily flow check, weekly pacing check, and monthly measures routine.
7. **Skills, taught once so they run as scripts.** Open a pull request from the template, fire a Claude Code task ([`templates/skills/fire-task.md`](templates/skills/fire-task.md)), run the suite and report, post a verdict, compile the sprint summary from milestone data, and on the fallback pool install the CLIs and dispatch to them ([`templates/skills/dispatch-cli.md`](templates/skills/dispatch-cli.md)). Each skill's text is also kept in the product repository under `/docs/skills/`.

## Executor pools

Three subscriptions supply executors, and the founder sets each pool up once. No sign-in is ever typed into a chat, and no plan has overage or extra credits turned on.

### Claude

1. **GitHub.** Connect GitHub to the founder's Claude account at claude.ai/code and install the Claude GitHub App on each product repository. Tasks clone through it and push their branches through the founder's connection, the way Cloud Agents push through Cursor.
2. **Cloud environment.** One per product repository: Trusted network access with the Grok Bot webhook host added to the allowed domains, a setup script mirroring `.cursor/environment.json` so no run spends its time installing, `CTO_WEBHOOK_URL` as an environment variable, and the webhook key as a network secret, so it is attached outside the sandbox and never enters a session.
3. **Three saved tasks** at claude.ai/code/routines, each with an API trigger and no schedule: Engineer from [`templates/tasks/engineer-task.md`](templates/tasks/engineer-task.md), QA from [`templates/tasks/qa-task.md`](templates/tasks/qa-task.md), and Brief, the Engineer prompt with Claude Opus 5.5 selected. Each prompt acts only on the launch packet in the `routine-fire-payload` block, pushes a branch, never opens a pull request, and ends by posting its report to the CTO webhook. Enter each trigger URL and token through the secure secret card into `/workspace/.secrets/claude-tasks.env` on the shared computer, mode 0600. The `fire-task` skill reads that file by path.
4. **Review workflow.** Copy [`templates/workflows/claude-review.yml`](templates/workflows/claude-review.yml) into the product repository's `.github/workflows/` and add the repository secret `CLAUDE_CODE_OAUTH_TOKEN`, generated by the founder with `claude setup-token` on their own machine. It runs only on pull requests whose `Models` line says `via Codex`, posts inline comments, and never approves. The token lasts a year, and the founder regenerates it.
5. **Limits.** A task accepts 30 fires an hour and the account 100, with no overage. Tasks share the plan's window with every other Claude session on the account, so the CTO reads the usage page at claude.ai/settings/usage before each launch.

### ChatGPT

1. **GitHub.** In Codex settings under the founder's ChatGPT account, connect each product repository, turn on automatic review with the trigger on pull request opened, and turn on auto security review. Codex posts P0 and P1 findings only, which is the first pass the Code Reviewer arbitrates. Bugbot stays off so there is one first pass.
2. **Rules.** The Reviewer's guidance goes in `AGENTS.md` under `## Code Review Rules`, which Codex reads, instead of `.cursor/BUGBOT.md`.
3. **Fix rounds.** The CTO posts `@codex fix` with the findings on the pull request, and Codex pushes to the branch. Verify on the first pull request that a comment from `shpdev-cto` triggers it. If only the founder's account does, the fallback is a relaunch on the pool that wrote the change.
4. **Metering.** GitHub reviews count under Codex's Code Review usage and fix runs under the plan's window. The CTO reads the usage page before relying on either.

### Shared computer, the fallback

Optional, and only after the two cloud pools exist. Everything signed in here is usable by every Bot on the account, and a computer Update, Recover, or Reset removes installed packages, so the install is a skill that reruns.

1. **Install skill.** Installs the Claude Code and Codex CLIs at pinned versions under `/workspace/tools`, fetches the role cards into `/workspace/cards`, and checks both CLIs with `--version`. The CTO reruns it after any computer update. No `sudo`, nothing outside `/workspace`.
2. **Sign-in.** Codex: the Bot runs `codex login --device-auth` and reports the URL and code, and the founder completes it in their own browser, after enabling device code login in ChatGPT's security settings. Claude: the founder generates a token with `claude setup-token` on their own machine and enters it through the secure secret card into `/workspace/.secrets/claude.env`, mode 0600, which the dispatch skill sources as `CLAUDE_CODE_OAUTH_TOKEN`. One Codex `auth.json` lives on this computer and nowhere else, and when Codex starts returning 401 the founder signs in again.
3. **Dispatch skill.** [`templates/skills/dispatch-cli.md`](templates/skills/dispatch-cli.md): `claude -p` or `codex exec` with the packet, in the background, ending with a POST of the report to the CTO webhook. Never `--bare`, which ignores the subscription token, and never a run in the foreground.
4. **Auto Review.** Always Allow: the install skill, the dispatch skill, and `git` under `/workspace/repos`. Ask First: `--dangerously-skip-permissions`, `danger-full-access`, or any path outside `/workspace`.
5. **Terms.** Anthropic permits the unmodified Claude Code binary signed in with the founder's own plan, including on a hosted computer, and permits nothing else to use that token. OpenAI documents a ChatGPT sign-in kept on a trusted private machine as an advanced option and recommends API keys for automation. This computer is private, one copy of the sign-in exists, and no run is ever started by outside content. Both plans' limits assume ordinary individual use, which is what the quota rule in the CTO card keeps this to.

## Product repository

Copy `.github/` from this repository, replace `sheepnir/GrokBot_DevFlow` in `.github/ISSUE_TEMPLATE/config.yml`, create the labels from `.github/labels.yml`, and connect the repository to Cursor with Bugbot off and Autofix off, since Codex review is the first pass, install the Claude GitHub App, and connect the repository in Codex settings. Then:

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
| C9 | Secrets | Secret scanning with push protection. Secrets in the secrets manager, Cursor's secret store, and the Claude cloud environment's network secrets. Before release day, dry-run that the process that needs a secret can see it. | A push with the provider's test pattern is blocked |

Also: `AGENTS.md` at the root with how to build, test, and run, and the conventions; `.cursor/rules/*.mdc` scoped by globs; `.cursor/environment.json` so no agent spends its run installing; a `## Code Review Rules` section in `AGENTS.md` with the Reviewer's guidance, which Codex reads; `.github/workflows/claude-review.yml` from `templates/workflows/` with the `CLAUDE_CODE_OAUTH_TOKEN` secret; monitoring and a cost alert that reaches the founder; the deployment and rollback procedure in `/docs/operations.md`; the folders `/docs/briefs`, `/docs/sprints`, `/docs/skills`, and the files `/docs/decisions.md` and `/docs/setup.md`; one milestone per sprint and no sprint Projects. Private deployment and credential details go in a private ops repository per product, never a public one.

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
