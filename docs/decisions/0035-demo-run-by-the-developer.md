---
status: accepted
date: 2026-10-29
deciders: RN (owner)
---
# The developer runs the demo, off the repo; the PM approves on the planning PR

## Context and problem statement

PRD §5 has the Demo stage produce a "short script and recording of the acceptance criteria running", approved by the PM. C6 (should have) generates it from the criteria on a preview environment "so the PM can accept without a meeting", and P7 places stages 6–8 in CI. Demos use the stage environment ([0011](0011-stage-environment-for-demos.md)). Committing recordings would bloat product repos, and GitHub has no official API for attaching files to PR comments. Who runs the demo, and where does it live?

## Considered options

* A. Recorded in CI: a job runs a browser or API script against stage, saves a video and screenshots per criterion as viewable CI artifacts, and posts the links on a demo PR
* B. Run by the developer: the command prepares the demo, the developer runs it and shares it as they choose
* C. No recording: the PM follows steps on stage next to the criteria results

## Decision outcome

Chosen option: B, set by the owner. Demos are usually given in a meeting. `/bagheera:demo` writes a short script from the approved criteria; the developer demos it live or records it and shares the recording however they like. Nothing about the demo is committed or pushed. The PM records acceptance with `/approved demo` on the feature's planning PR, which is what Close waits for.

### Consequences

* Good, because there is nothing to set up in CI: no access from CI to stage, no test accounts as CI secrets, no recordings to store.
* Good, because developers demo the way their team already does.
* Good, because acceptance is still recorded on GitHub, where the dashboard and Close can see it (PRD G4: approve "where the work happens").
* Bad, because accepting without a meeting (C6) becomes optional: it happens only when the developer shares a recording.
* Bad, because the demo runs on the developer's machine, not in CI as P7 says for stages 6–8.
* The PRD's wording for §5 stage 7, C6 and P7 should change to match; listed for the owner in the design index.

## More information

Detail in [design/developer-workflow.md, section 7](../design/developer-workflow.md#7-demo).
