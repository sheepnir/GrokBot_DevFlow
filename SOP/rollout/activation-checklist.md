# Workflow v2 activation and pilot

Owner: CTO, with the founder. Status: prepared, not executed. This is the rollout record for SM-016 and the SM-017 hardening changes. Mark a step complete only with actual evidence. Repository documentation and a merged PR do not prove a live setting or a saved Bot instruction changed.

## Current handoff

- The founder requested completion of the rollout on 2026-09-26. That authorizes preparation and implementation, but is not a recorded CTO approval.
- Versioned profiles and shared rules are in [profiles](../profiles/README.md). They were prepared from repository policy; the existing live profiles have not been exported or compared.
- A product repository, live Bot configuration location, and deployment target have not been identified for this rollout. No product controls, live profile installation, watchdog, deployment, or pilot are claimed as complete.
- The prior approved process remains effective until approvals and instruction installation are recorded. The [SOP index](../README.md) remains the authoritative approval record.

## 1. Synchronize instructions and activate

1. Identify the ten live Bots (CTO plus nine roles), their saved profiles, shared collaboration rules, and existing routines. Record non-secret identifiers and the previous configuration version in a private administration record. Do not publish private prompts or memory exports.
2. Compare the live profiles with [the proposed profiles](../profiles/README.md). Preserve compatible role-specific knowledge and resolve conflicting instructions about sprints, points, quality caps, engineer merges, QA on every PR, retries, runtime limits, and release authority. Keep the start rule on every profile.
3. Obtain the CTO and founder's actual decisions on the exact revision. Record their dates and decision evidence in SM-016/SM-017 and update the affected SOP statuses together. Do not sign for either person or infer approval from a merge.
4. Pause new work during instruction replacement; checkpoint active work and record which approved version governs it. Save each role profile plus the shared rules. Read back the saved configuration and record the installed repository commit for each Bot. Do not send configuration text as a live task.
5. Update existing routines in place: daily flow check, weekly pacing, monthly measures/spending, and infrastructure check. Remove superseded sprint routines, avoid duplicates, and preserve notification intent. Runtime stopping is a separate per-run control, not a daily routine. Record trigger, timezone, owner, and output for each routine.
6. Check each Bot can explain its tier-dependent role, GitHub permissions, and stop conditions without performing external actions. In particular, verify the reviewer cannot author its own change and other Bots request GitHub writes through the CTO.
7. Record the installed versions and checks in the table below. Activate the revision only once all instruction rows and approvals are complete; then update the root README status and effective date. If installation fails, restore the previous saved versions before resuming work.

| Bot | Proposed profile | Saved version matches | Evidence / installed commit |
|---|---|---|---|
| CTO | [cto](../profiles/cto.md) | Not verified | Pending live access |
| PM | [product-manager](../profiles/product-manager.md) | Not verified | Pending live access |
| Designer | [designer](../profiles/designer.md) | Not verified | Pending live access |
| Architect | [architect](../profiles/architect.md) | Not verified | Pending live access |
| Scrum Master | [scrum-master](../profiles/scrum-master.md) | Not verified | Pending live access |
| Cloud Engineer | [cloud-engineer](../profiles/cloud-engineer.md) | Not verified | Pending live access |
| Frontend Engineer | [frontend-engineer](../profiles/frontend-engineer.md) | Not verified | Pending live access |
| Backend Engineer | [backend-engineer](../profiles/backend-engineer.md) | Not verified | Pending live access |
| Code Reviewer | [code-reviewer](../profiles/code-reviewer.md) | Not verified | Pending live access |
| QA | [qa](../profiles/qa.md) | Not verified | Pending live access |

## 2. Configure and verify the first product repository

Run [SOP-004](../SOP-004-new-product-repository.md). Its bootstrap exception allows setup issues before the controls they create exist. Copy this section into `/docs/SM/setup.md`, record the product repository and target, and attach results. A setting is not enforced until its negative test has passed. Tests use disposable branches, synthetic data, and non-production cloud resources; deployment-protection tests must not deploy real production code.

| Control / behavior | Verification | Expected result | Evidence |
|---|---|---|---|
| C1 PR-only main | Attempt direct push and force push with each execution identity | Both blocked; no bypass authority available to build agents | Not run |
| C2 independent review | Attempt merge with no review, then with only an author's review | Both blocked; a valid reviewer approval permits merge only when other checks pass | Not run |
| C3 stale approval | Approve a test PR, then push another commit | Earlier approval dismissed; merge blocked until new review | Not run |
| C4 CI | Fail the required check and test a branch behind main | Merge blocked until required checks pass on the required state | Not run |
| C5 QA (manual) | Present missing QA evidence or evidence for an older SHA on a tier requiring QA | Reviewer withholds approval; current-SHA verdict required | Not run |
| C6 specialist review (manual) | Omit a risk flag on a synthetic sensitive-path change or omit specialist evidence | Reviewer adds the flag and withholds approval until the required review exists | Not run |
| C7 production authorization | Start a harmless protected job; attempt self-approval, non-main deployment, and test-environment access to the production role | Unauthorized paths blocked; the other authorized identity can approve; no real deployment in this test | Not run |
| C8 flags | Test unfinished behavior with production-default flags off, then on in test only | No exposed unfinished behavior; production flag enablement follows release approval | Not run |
| C9 secret scanning | Use the provider's documented harmless test fixture for a supported secret pattern | Push blocked without using an actual credential; supported-pattern coverage is not a guarantee against all leaks | Not run |
| Identity permissions | Inspect actual execution credentials and attempt prohibited actions in the test target | Document which limits are tool-enforced and which are written policy; do not call a founder-scoped credential branch-only without evidence | Not run |
| Claim serialization | Queue two owners for one issue and interrupt a claim between writes | One acknowledged owner and one build; partial state reconciled before launch | Not run |
| Dispatcher takeover | Simulate an unavailable dispatcher with an uncertain active run | New claims wait until stop and state reconciliation are confirmed | Not run |
| Operation retry | Create launch/result notes with separate IDs, then retry one; simulate a timeout after a successful write | Both intended notes exist once; retry returns existing object; uncertain outcome does not produce a duplicate | Not run |
| Runtime enforcement | Run a harmless agent with a shortened test deadline; test loss of stopping mechanism | Save window respected, stop confirmed within the rule's bound; unattended launch blocked without working enforcement | Not run |
| Desired-state retry | Retry an already confirmed artifact/configuration, then change only flag/configuration revision | Exact retry is a no-op; changed configuration is applied despite unchanged code SHA | Not run |
| Rollback | In test, deploy healthy A then failing B and invoke rollback | Restore recorded A and its compatible configuration, not the newest tag; reject arbitrary rollback targets | Not run |
| Migration recovery | Simulate partial migration, incompatible schema, and a first deployment with no predecessor | No blind retry or unsafe rollback; containment and explicit recovery path used | Not run |
| Deployment concurrency | Request normal deployment and rollback concurrently | One environment mutation at a time; state reconciled after failure | Not run |

Register the dispatcher session, durable operation-ledger location, timeout/watchdog (or supervisor), and test evidence in `setup.md`. A manual procedure remains labeled manual even after a successful rehearsal. No new accounts, paid services, or on-demand spending are implied by this checklist.

## 3. Run the pilot

Choose real backlog items after the product repository is ready. Do not invent product requirements or claim documentation examples are delivered features.

1. **Small change:** one narrow fix with a regression test, owner, independent reviewer, CI, and completion evidence. No BRD/TDD or QA run unless a risk flag requires one.
2. **Standard feature:** an actual feature with only missing requirements/design documented, author evidence, independent review and QA, applicable acceptance, and a production-off flag if merging unfinished behavior.
3. Rehearse a **small security-sensitive classification** using synthetic code in the test target: confirm security review and QA are required despite Small size. This is a control exercise, not an invented production feature.
4. Record links to the issues, PRs, run IDs, checks, verdicts, actual approvals, release (if authorized), and completion. Record delivery time, waiting time, defects, and actual usage where available; unknown usage stays unknown.
5. Assess whether CTO-mediated writes and founder participation in every production deployment cause meaningful delay. Keep those authority rules unless the founder and CTO explicitly change them. Change only what the evidence warrants; do not add a recurring ceremony.

| Pilot | Issue / PR | Result | Evidence |
|---|---|---|---|
| Small change | Awaiting product backlog | Not run | Pending target |
| Standard feature | Awaiting product backlog | Not run | Pending target |
| Sensitive Small rehearsal | Awaiting test target | Not run | Pending target |
