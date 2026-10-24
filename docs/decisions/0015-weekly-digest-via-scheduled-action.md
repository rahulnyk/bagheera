---
status: proposed
date: 2026-10-24
decision-makers: RN
---

# Weekly digest: a scheduled claude-code-action job, not a custom Agent SDK program

## Context and Problem Statement

The architecture describes "a scheduled Agent SDK job" that reads dashboard data and the week's `features/` log and posts a one-page summary to Slack.

## Considered Options

* A. `on: schedule` GitHub workflow running claude-code-action (built on the Agent SDK) with a prompt; data pulled by `curl` from Grafana/Prometheus HTTP APIs into files first; posted with a Slack incoming webhook
* B. A Python program on the Claude Agent SDK, deployed somewhere with a scheduler

## Decision Outcome

Chosen option: "A", because it is the same runtime with no program to host, and it reuses the CI auth from [0011](0011-ci-auth-and-models.md).

### Consequences

* Good, because zero code beyond a workflow file and a prompt.
* Bad, because Slack posting from a workflow needs a webhook secret; fine for one channel.

### Confirmation

Phase 4 test: a manual `workflow_dispatch` posts a digest naming at least one shipped feature and one stuck gate from the sample data.

## More Information

Revisit B only if the digest needs interactive follow-up or many channels.
