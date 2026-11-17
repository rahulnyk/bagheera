# 4. PMs draft in Claude Desktop, with the PRD in a Jira issue

* Status: accepted
* Deciders: RN
* Date: 2026-11-17

## Context and Problem Statement

PMs need help drafting a PRD checked against the wiki and the code (R1–R6), without git or GitHub's pull requests. They keep documents in tools of their own choosing. Approving must happen where they already work.

## Considered Options

* A Claude Desktop plugin using the GitHub and Atlassian connectors; the PRD lives in a Jira issue
* A drafting page in the portal, with chat and a Markdown editor
* A Slack or Teams bot

## Decision Outcome

Chosen option: the Claude Desktop plugin. Nothing is built for drafting beyond skills and a template. PMs get read-only GitHub access so the plugin can search the wiki and code. The PRD's Jira issue holds the draft, its questions and its approval. The PM copies the PRD into any tool they like.

### Consequences

* Good: no drafting UI or server to build; PMs stay in Claude Desktop and Jira.
* Good: Jira records who approved and when, and notifies people of questions.
* Bad: each PM needs a read-only GitHub seat.
* Bad: a copy edited in another tool drifts; changes go back through the Jira issue.
