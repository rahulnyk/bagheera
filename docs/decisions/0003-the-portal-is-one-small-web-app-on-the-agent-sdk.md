---
status: proposed
date: 2026-11-14
deciders: RN (owner)
---
# The portal is one small web app that runs the plugin's skills on the Claude Agent SDK

## Context and problem statement

PMs work in a portal ([0002](0002-pms-work-in-a-portal.md)). What is it built from, what does it run, and what does it need from the org?

## Considered options

* A. One small web app of our own, running the plugin's `spec` skill on the Claude Agent SDK
* B. A Jira (Forge) app. Forge can't run Claude Code over a repo checkout, so it can't check a spec against the code
* C. A Backstage plugin. It's heavy to run and developer-facing; right only if the org already runs Backstage
* D. Claude (Cowork) with the plugin, and no portal of our own. Every PM would need a GitHub connector, and there'd be no shared board

## Decision outcome

Proposed: A.

* **Two screens:** the board, and the feature page. The portal runs Spec. It shows Plan and Build, which developers run in Claude Code. It also merges each ticket's wiki PR when its code merges ([0020](0020-wiki-changes-are-reviewed-with-the-code-and-merged-by-the-portal.md)).
* **One skill, two front doors.** The portal loads the same `spec` skill as `/bagheera:spec`, so the two can't drift apart.
* **Locked down.** Sign-in is through the org's SSO. The drafting agent reads a clone of the repo and writes only `spec.md`, with no shell and no web. The portal's own code commits and merges, through its GitHub App, on onboarded repos and the wiki only.
* **Jira.** A service account reads tickets and linked pages. It comments on the ticket, mentioning the person, whenever a feature starts waiting on them.
* **No database.** GitHub is the record.

### Consequences

* Good, because PMs need only a browser and their SSO sign-in.
* Good, because the portal needs no logic of its own for drafting.
* Bad, because it needs org admins once: a host, an SSO client, the GitHub App, a Jira service account and model access.
* Bad, because developers can't run Plan or Build from the browser yet. That's a [later addition](../plan.md#5-later-additions), with its own signal.
