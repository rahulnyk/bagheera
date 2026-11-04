---
status: accepted
date: 2026-10-26
deciders: RN (owner)
---
# Start with a view-only dashboard fed by machine-written status data

## Context and problem statement

Leadership, PMs, tech leads and QA need one list of stories with filters and "waiting on me" (PRD V1–V4). PRD Q3 asks whether the dashboard is view-only or lets people act.

## Considered options

* View-only, with links to the PR waiting for approval and the stage documents
* Interactive: approve, nudge or reassign from the dashboard

## Decision outcome

Chosen option: view-only at first, with links to the PR waiting for approval and to the approval documents. V1–V4 stand as written. The dashboard is fed by status data kept alongside the PRs and documents, or in a separate folder; where that data lives and what the dashboard is built on is open, to brainstorm in design doc 5.

### Consequences

* Good, because acting stays in GitHub, where approvals already happen.
* Note: V4 still applies, so the status data must be written by tools, never typed by hand.
