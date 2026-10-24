---
status: proposed
date: 2026-10-24
decision-makers: RN, pilot PM
---

# Delivery progress: sync front-matter to Jira with an existing Spec Kit Jira extension; Grafana reads Jira and GitHub through existing data sources

## Context and Problem Statement

The architecture says don't build a tracker: artifact front-matter is the state, a small CI job syncs it to Jira, and a Grafana panel queries GitHub and Jira. Grafana's native Jira data source is an Enterprise plugin, and the sync job is listed as something we build.

## Decision Drivers

* Build as little as possible
* Deterministic sync (no model in the loop for status updates)
* Works on open-source Grafana

## Considered Options

* Sync: A. a community Spec Kit Jira extension (`spec-kit-jira-sync`: idempotent, drift-aware, fail-closed; Epic per repo, Story per spec, Subtask per phase), run in CI on push to `specs/**`; B. a ~100-line script using the Jira REST API; C. claude-code-action with the Atlassian MCP server
* Dashboard: 1. Grafana OSS with the GitHub data source and the Infinity data source (JSON over Jira REST/JQL); 2. Grafana Enterprise Jira data source

## Decision Outcome

Chosen: "A" (after a security review of the extension), falling back to "B"; and "1".

### Consequences

* Good, because the sync may be zero code.
* Bad, because A is a single-maintainer community project; we pin a commit and review it like our own code, or fork it.
* Bad, because "time waiting at each gate" needs Jira status-change history; Infinity can read the changelog API but the panel query is fiddly. Acceptable for one dashboard.

### Confirmation

Phase 4 test: moving a sample spec from `draft` to `approved` on `main` moves the Jira story within 5 minutes; the dashboard shows it in the right stage.

## More Information

Depends on Jira. Under Linear, swap the extension and data-source query. Decided in Phase 4; recorded now so Phase 2 front-matter matches what the sync needs.
