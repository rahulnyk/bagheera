---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# No spend caps until after the pilot

## Context and problem statement

PRD D4: CI spend is capped per job and reported separately from seat usage. Nobody knows yet what a sensible cap would be. Design caps now, or decide after the pilot?

## Considered options

* A. Per-run caps and a monthly limit, with placeholder numbers
* B. No caps for now; record every run's cost, and decide after the pilot whether to cap and at what level

## Decision outcome

Set by the owner: B. Caps are a complication to add after experimenting, not to plan beforehand.

* **Reported, not capped.** Each Verify and library-update run records its cost, and the weekly summary adds them up by repository ([0066](0066-report-job-spend-by-repository.md)).
* **No caps anywhere for now:** none per run, none per month, and none on Baloo's runs in the library update.
* **After the pilot** (`docs/plan.md`), the owner decides from the recorded costs whether to cap, and if so how.

### Consequences

* Good, because there's nothing to guess or tune before there's data.
* Bad, because a run that loops or reads far too much isn't stopped automatically; the recorded costs show it afterwards.
* Bad, because PRD D4's "capped per job" isn't met until the decision after the pilot.

## More information

Detail in [design/distribution-security-and-cost.md, section 4](../design/distribution-security-and-cost.md#4-spend-limits-and-reporting).
