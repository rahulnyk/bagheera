---
status: proposed
date: 2026-10-24
decision-makers: RN, platform
---

# Attribute telemetry to repo and team at launch; attribute to features through commit events, not a SessionStart hook

## Context and Problem Statement

The architecture says to add `team` and `feature_id` resource attributes and that "the SessionStart hook can set them from the branch name". Checked against the Claude Code monitoring docs (Oct 2026): resource attributes are read from the environment when Claude Code starts, hooks can't change them mid-session, there is no branch attribute on metrics or events, and project settings files can't set telemetry export variables. So the hook approach won't work as written.

## Decision Drivers

* Cost and usage per team and per feature (Layer 4)
* No wrapper scripts that 100 engineers must install
* Low label cardinality in Prometheus

## Considered Options

* A. Managed settings turn on `OTEL_METRICS_INCLUDE_REPOSITORY=true` (repo identity on every datapoint) and `OTEL_LOG_TOOL_DETAILS=1` (branch on `git commit` events); team comes from a repo→team map in Grafana built from service pages' `owners`; feature comes from joining `session.id` to the branch on commit events, in Loki
* B. A shell wrapper around `claude` that exports `OTEL_RESOURCE_ATTRIBUTES=team=…,feature_id=…` from the branch
* C. The SessionStart hook sends its own OTLP log record mapping `session.id` → branch/ticket to the collector

## Decision Outcome

Chosen option: "A", with C as an add-on if A's commit-based join misses too many sessions (sessions with no commit).

### Consequences

* Good, because no code and nothing to install on laptops; all from managed settings.
* Good, because repo and team are low-cardinality labels; feature stays in logs, not metric labels.
* Bad, because sessions that never commit don't map to a feature. C (about 20 lines in the hook, using `curl` to the collector's OTLP/HTTP endpoint) closes that gap if needed.
* Bad, because the architecture text needs correcting.

### Confirmation

Phase 1 test T1.12: after a session on branch `JIRA-123-x` with one commit, Grafana shows the session's cost under repo, team and feature `JIRA-123`.

## Pros and Cons of the Options

### B. Wrapper

* Bad, because IDE and desktop launches bypass shell wrappers, and every engineer must install it.

### C. Hook-sent mapping

* Good, because covers sessions without commits.
* Bad, because small custom code and a collector endpoint reachable from laptops.

## More Information

https://code.claude.com/docs/en/monitoring-usage. `OTEL_METRICS_INCLUDE_REPOSITORY` needs Claude Code v2.1.269+; pin a minimum version in managed settings (`requiredMinimumVersion`).
