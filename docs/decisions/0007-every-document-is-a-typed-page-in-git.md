---
status: accepted
date: 2026-11-14
deciders: RN (owner)
---
# Every document is a typed page, checked into git

## Context and problem statement

The knowledge flywheel is what makes Bagheera worth adopting: work documents in the repo, a strong structure for each, and a wiki that grows with every feature. How do we keep it while simplifying everything else?

## Decision outcome

Set by the owner: keep it.

* Specs, plans, service pages, features, decisions and standards each have a page type: Markdown with a YAML header (`type`, `title`, `updated`, plus the type's own fields) and fixed sections.
* Specs and plans are checked into the code repo, in the ticket's PR. Everything else is in the wiki ([0008](0008-one-wiki-for-the-org.md)).
* One page check, in the plugin, runs before every commit Bagheera makes, from Claude Code or the portal. It checks fields, sections, caps ([0009](0009-hard-limits-on-length.md)), links and IDs.
* Git history is the change log. Pages are rewritten in place.

### Consequences

* Good, because people, Claude and scripts can all read every document.
* Good, because status can be worked out from the files.
* Bad, because hand edits are checked only the next time a command runs in the repo. With nothing in CI ([0010](0010-nothing-runs-in-ci-in-the-mvp.md)), that's accepted.
