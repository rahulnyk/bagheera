---
status: proposed
date: 2026-10-24
decision-makers: RN
---

# Record harness decisions as MADR files in docs/decisions

## Context and Problem Statement

The harness has many design choices that reviewers (engineers, PMs, security) need to see with their reasons, and the architecture already says org-level decisions use MADR in the wiki's `decisions/` (the architecture's `org-wiki`, here `council-rock`). Where and how do we record decisions about the harness itself?

## Decision Drivers

* Same format as the wiki's `decisions/` pages, so people learn one format
* Reviewable in PRs, next to the plan
* No new tooling

## Considered Options

* MADR 4.0 files in `docs/decisions/`
* Nygard-style ADRs
* Decisions inline in `docs/plan.md` only

## Decision Outcome

Chosen option: "MADR 4.0 files in `docs/decisions/`", because the architecture mandates MADR for org decisions and one file per decision keeps each reviewable on its own.

### Consequences

* Good, because each decision has options and reasons, not only the result.
* Good, because these files can move into `council-rock/decisions/` once that repo exists (see [0002](0002-repository-topology.md)).
* Bad, because harness ADRs use MADR's own front-matter here, while wiki ADRs will use the wiki's OKF front-matter ([0003](0003-wiki-page-format-okf.md)); moving them needs a small conversion.

### Confirmation

Every "Decision" row in `docs/plan.md` links to a file here. Status moves from `proposed` to `accepted` only after review.

## Pros and Cons of the Options

### Nygard-style ADRs

* Good, because shorter.
* Bad, because no "considered options" section, which is the part reviewers need most here.

### Inline in the plan

* Good, because one file.
* Bad, because decisions get buried and can't be superseded individually.

## More Information

MADR: https://adr.github.io/madr/. Template: [adr-template.md](adr-template.md).
