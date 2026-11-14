---
status: accepted
date: 2026-11-14
deciders: RN (owner)
---
# Review every change to Bagheera by PR; publish the plugin through the org's marketplace

## Context and problem statement

Bagheera runs on laptops and in the portal with real access to code and tickets. How are changes reviewed and shipped?

## Decision outcome

Chosen by the owner.

* Every change, to the plugin or the portal, is a PR to `main` in `seeonee/bagheera`. The other platform engineer approves it, and it's tried on the test repo first. There's no fast lane.
* Changes to the settings template also need security's approval, and the council's for its deny rules. Changes to the portal's access also need security's approval.
* A plugin release is a version bump in `plugin.json`. The org's marketplace publishes it under its own policies, which are outside Bagheera's scope. Rolling back is a new version.
* The portal deploys from `main`, and pins the plugin version it runs.

### Consequences

* Good, because nothing reaches laptops or the portal unreviewed.
* Bad, because changes wait when one platform engineer is away.
