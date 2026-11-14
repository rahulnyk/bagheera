---
status: accepted
date: 2026-11-14
deciders: RN (owner)
---
# Seed each repo's service pages once, from its code, with our own skill

## Context and problem statement

Existing repos have no service pages, so their first tickets would start without context. How do the first pages get written?

## Decision outcome

Chosen by the owner.

* An engineer on the team runs `/bagheera:seed` once per repo, before its first ticket.
* It follows DeepWiki-Open's method. It plans from the file tree and README, and the engineer confirms the plan. A read-only reader then writes each page only from the planned files.
* It reads code, config and the repo's own docs. It skips vendored code, build output, lock files, and env and secret files, and doesn't follow links out of the org.
* Where docs and code disagree, the page follows the code, and the PR lists the conflict.
* A dependency with no page yet is named with its repo. Third-party services are named in plain words.
* It opens one PR, listing the files behind each section, and the team reviews it as an onboarding task.

### Consequences

* Good, because a repo's first ticket starts with a service page.
* Bad, because draft quality is unknown until it runs on a real repo, so we try it early.
