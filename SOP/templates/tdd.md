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

Authentication, authorization, personal data, and secrets touched by this design. Whether the Architect's security check is required for the resulting changes.

## Reliability and performance

Failure modes, timeouts, retries, and the load this must handle. What's monitored.

## Testing

What the engineers test, what QA verifies, and any test data or environment this needs.

## Rollout

Feature flags, migrations order, and rollback.

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
