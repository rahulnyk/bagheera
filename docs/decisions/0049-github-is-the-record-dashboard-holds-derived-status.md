---
status: accepted
date: 2026-10-30
deciders: RN (owner)
---
# GitHub is the record; the dashboard holds the derived status

## Context and problem statement

The dashboard shows each story's stage, status, who it is waiting on and for how long, and phase progress (PRD V2). Status must be derived from the artifacts and PRs, never entered by hand (V4). Nothing shared may be committed to product repos ([0030](0030-one-planning-pr-per-feature.md)), and approvals, overrides, dismissals and demo approvals already live on GitHub PRs. [0005](0005-view-only-dashboard.md) left open where the derived status lives.

## Considered options

* A. GitHub is the record: the sync job derives each story's status from PRs, comments, checks and files every 15 minutes and writes it only into the dashboard
* B. A status folder in the library, with one file per story written by the sync job
* C. A status block in each story's planning PR description, which the dashboard reads

## Decision outcome

Chosen option: A. One dashboard row per story, keyed on its planning PR. Durations come from GitHub's own timestamps; history and throughput are computed when needed from merged PRs and the library's feature entries. Dashboard fields are written only by the sync job, so a wrong value is fixed by fixing the rule or the work, and the next sync overwrites it.

### Consequences

* Good, because status stays derived from the work, with one place to look and nothing new to store.
* Good, because product repos and the library stay free of status data; this settles the open question of status in the library (PRD K1's page types are unchanged).
* Bad, because the dashboard lags the work by up to 15 minutes.
* Bad, because trends must be recomputed from GitHub rather than read from a stored history.

## More information

Detail in [design/status-and-dashboard.md, section 1](../design/status-and-dashboard.md#1-where-status-data-lives).
