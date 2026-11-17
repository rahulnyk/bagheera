# 2. Service catalog as wiki pages

* Status: accepted
* Deciders: RN
* Date: 2026-11-17

## Context and Problem Statement

PMs and Claude need to know which services exist, who owns them and how they depend on each other (K1, K2, R5). Where is that catalog kept?

## Considered Options

* One typed page per service in the wiki, with its dependencies in the header
* A service catalog tool such as Backstage

## Decision Outcome

Chosen option: wiki pages, because the catalog then lives in the same graph as features and decisions, with nothing extra to host. Ops views (health, infrastructure, on-call) are out of scope.

### Consequences

* Good: a service page links to what it depends on; what depends on it is found by searching the other pages' headers.
* Bad: no runtime or ops views; teams keep their existing ops tools for those.
