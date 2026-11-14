# Org adoption

_Proposal for the owner's review, 2026-11-14 · [PRD](../prd.md) · [Plan](../plan.md)_

These parts are for an org that adopts Bagheera widely. They need org admins or agreement across teams: the PRD's **L** requirements. Each one comes in on its own, when its signal shows. The commands and the portal stay as they are.

| Part | What it adds | Needs | Signal |
| --- | --- | --- | --- |
| **CI** (P6, K9) | The same commands in CI: Verify on every PR, and the wiki update as a workflow in place of `/bagheera:update-wiki`, covering PRs made without Bagheera too | Org rulesets and CI secrets; agreement on what blocks a merge | Reviewers across teams catch standards problems by hand; wiki pages drift |
| **CI credentials and spend caps** (D3) | Short-lived credentials (OIDC) for the model provider; per-run and monthly spend caps | The Claude admin | CI comes in |
| **Managed settings** (R5, D4) | Deny rules and the plugin locked for everyone; only approved plugins allowed | A Claude org owner | Keeping every repo's settings file in step gets hard |
| **Adoption and cost** (V6) | Cost by repo and feature on the board | Usage records from CI and the portal | Leadership asks what it costs |
| **A portable page format** | Pages checked against the Open Knowledge Format | Agreement with the owners of other tools | Another tool needs to read the wiki |
