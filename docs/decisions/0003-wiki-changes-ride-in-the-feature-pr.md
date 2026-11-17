# 3. Wiki changes ride in the feature PR; the portal applies them on merge

* Status: accepted
* Deciders: RN
* Date: 2026-11-17

## Context and Problem Statement

Every merged piece of work must update the wiki (K5), a person must review every wiki change (K6), and nothing in the first release may depend on CI.

## Considered Options

* Wiki pages edited in the feature PR; the portal copies them to the wiki on merge
* A second PR in the wiki repo for every piece of work
* An agent that updates the wiki after merge, with no review
* A GitHub Action that copies the pages on merge

## Decision Outcome

Chosen option: pages in the feature PR, copied by the portal. The developer and reviewer handle one PR, the reviewed text is exactly what lands in the wiki, and the portal already receives GitHub webhooks, so no CI is needed.

### Consequences

* Good: one PR and one review; no agent writes to the wiki unreviewed.
* Good: the portal also posts the check that blocks merging without a wiki update.
* Bad: the portal's GitHub App needs write access to the wiki repo.
* Accepted: two merges that edit the same page at once; the later one wins, and git keeps both.
