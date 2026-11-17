# 6. One portal for status

* Status: accepted
* Deciders: RN
* Date: 2026-11-17

## Context and Problem Statement

PMs, developers and leadership need one signed-in place to see where all work stands, with status coming from the work itself (V1–V6). Something must also receive GitHub and Jira webhooks.

## Considered Options

* One small web app (Next.js) that reads GitHub and Jira and receives their webhooks
* BI dashboards (Grafana, Metabase) with an ETL pipeline
* Jira dashboards: can't see specs, plans or test results

## Decision Outcome

Chosen option: one web app. It keeps no database: documents live in git and Jira, and status is read from them when a page loads.

### Consequences

* Good: one place for everyone; nobody types status.
* Bad: one container, a GitHub App and a webhook endpoint to host and run.
