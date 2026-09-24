<!-- Required for Large work. For Standard work, write a TDD section or a decision record only if the approach isn't obvious. Small work needs none. See SOP-006. -->

# TDD-NNN: <feature name>

| | |
|---|---|
| Owner | Software Architect |
| Status | Draft |
| Version | 0.1 |
| Accepted | |
| BRD | Link |
| Requirements covered | REQ IDs, all of them |

## Summary

What's being built, in a paragraph, and the shape of the solution.

## Context

The current architecture this fits into, with a link to `/docs/architect/architecture.md`. Constraints the design must respect.

## Design

One subsection per requirement or group of requirements. Each starts with the IDs it covers.

### REQ-<AREA>-001, REQ-<AREA>-002: <topic>

- Approach:
- Components and their responsibilities:
- Data: schema changes, migrations, and how they're made safe
- APIs: the contract the Frontend and Backend Engineers agreed, or a link to it
- Authorization: what's checked server-side, and where

## Security

Authentication, authorization, personal data, and secrets touched by this design, and the risk flags the resulting issues carry.

## Reliability and performance

Failure modes, timeouts, retries, and the load this must handle. What's monitored.

## Testing

What the engineers test, what QA verifies, and any test data or environment this needs.

## Rollout

The feature flag that keeps unfinished work off in production, the order of migrations and whether each one is additive, and the rollback. See SOP-010.

## Decisions

Links to `ADR-NNN` records made for this design.

## Open questions

| Question | Owner | Needed by |
|---|---|---|

## Acceptance

| Role | Decision | Date |
|---|---|---|
| Software Architect | | |
| Product Manager | | |
