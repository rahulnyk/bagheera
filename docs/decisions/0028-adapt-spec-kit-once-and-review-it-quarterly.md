---
status: accepted
date: 2026-10-29
deciders: RN (owner)
---
# Adapt Spec Kit's content once, and review its changes every quarter

## Context and problem statement

We copy Spec Kit's content into the plugin as our own stage commands ([0004](0004-carry-spec-kit-in-the-plugin.md)). What exactly do we copy, and how do we follow Spec Kit's changes? Spec Kit v1.2.5 (28 Oct 2026) releases every 3–5 days, mostly changes to its installer, extensions and presets. Since April 2026, the parts we would copy changed as follows: document templates 6 times, command instructions 38 times, helper scripts 42 times.

## Considered options

* A. Adapt once: copy the document templates and the instructions of six commands, rewritten as our stage commands; drop the extension hooks, the support for other AI tools and the helper scripts; review Spec Kit's changes to the copied files once a quarter
* B. Keep an unchanged copy at a pinned version and generate our commands by applying our edits as patches
* C. Adapt once and never follow updates

## Decision outcome

Chosen option: A. We copy the spec, plan and tasks templates and the instructions of `specify`, `clarify`, `plan`, `tasks`, `implement` and `analyze`, and adapt them to our stages. We record the Spec Kit version and which of our files came from which of theirs, and keep Spec Kit's MIT licence notice. Spec Kit's helper scripts are not copied; one short script of our own finds or creates the feature's folder and branch and lists its stage documents. Once a quarter, a platform engineer reads Spec Kit's changes to the source files since the recorded version and ports what helps.

This narrows [0004](0004-carry-spec-kit-in-the-plugin.md), which also listed the helper scripts.

### Consequences

* Good, because we keep Spec Kit's method and templates, which are most of its value, without its machinery.
* Good, because the upkeep is one planned review per quarter, with no build step.
* Bad, because improvements reach us up to a quarter late, and only when someone ports them.
* Bad, because our commands drift from Spec Kit's over time, so each review takes judgement, not just copying.

## More information

Detail in [design/developer-workflow.md, section 1](../design/developer-workflow.md#1-where-the-stage-commands-come-from).
