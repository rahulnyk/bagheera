# Glossary

Bageera's parts are named after characters and places from Kipling's *The Jungle Book*, so the names hang together and hint at each part's job. (Kipling spells the panther *Bagheera*; the project keeps the spelling `bageera`.)

**Rule:** repos, the plugin, agents, bots and apps get Jungle Book names. Skills, commands, hooks, scripts, workflows and files keep plain names that say what they do (`/feature start`, `wiki-lint`, `session-digest.sh`), because people type and read those every day.

## Repos and products

| Name | What it is | Architecture's term | Why the name |
| --- | --- | --- | --- |
| `bageera` | The project: plan, decisions, architecture. Also the name of the Claude Code plugin | "the harness", "the plugin" | Bagheera guides Mowgli through the jungle; the harness guides engineers through the SDLC |
| `seeonee` | Placeholder for your GitHub org | the placeholder org | The Seeonee wolf pack, Mowgli's community |
| `council-rock` | The org wiki repo | `org-wiki` | Where the pack meets to hear the Law and decide; the wiki holds the org's rules and decisions |
| `pack` | The plugin marketplace repo (plugin, Spec Kit preset and extension, reusable workflows, managed-settings source) | "internal marketplace repo" | Everything the pack shares; plugin id `bageera@pack` |
| `chil` | The monitoring repo (OTel Collector, Prometheus, Loki, Grafana) | Layer 4 | Chil the kite watches the whole jungle from the sky |
| "the Law" | Informal name for the wiki's `org/` pages | standards, dos and don'ts | The Law of the Jungle, which Baloo teaches |

## Agents (subagents in the plugin)

| Name | Role | Architecture's term | Why the name |
| --- | --- | --- | --- |
| `baloo` | Wiki curator: turns merged PRs and digests into wiki PRs | `wiki-curator` | Baloo keeps and teaches the Law |
| `kaa` | Architect: writes design artifacts under `specs/` only | `architect` | Old, wise, sees the whole shape of things |
| `mowgli` | Implementer: builds one phase in its own worktree | `implementer` | Does the work, learning as he goes |
| `grey-brother` | Test writer: writes tests from the test plan first | `test-writer` | Mowgli's wolf brother, who watches his back |
| `akela` | Reviewer: standards and security, read-only | `reviewer` | Leader of the pack, who judges at Council Rock |
| `hathi` | Demo builder: walks acceptance flows and narrates a demo | `demo-builder` | Hathi the elephant tells the jungle's stories |

## Bots and identities

| Name | What it is |
| --- | --- |
| `baloo-bot` | GitHub App the curator acts as when it opens wiki PRs |
| `process:baloo` | Identity written into wiki page front-matter (`generated.by`) when the curator writes a page |

## Kept plain on purpose

Stage commands (`/feature start | architect | plan | testplan | build | demo`), skills (`org-standards`, `deploy-config`, `wiki-lookup`, `wiki-lint`), hooks and their scripts, workflow files (`wiki-curate.yml`, `harness-review.yml`, …), artifact files (`spec.md`, `plan.md`, `tasks.md`, `testplan.md`, `demo.md`) and wiki folders (`org/`, `services/`, `decisions/`, `features/`, `raw/`).

Spare names if we need more: Shere Khan (a threat model or red-team suite), Tabaqui (the jackal; noise or spam filter), Ikki (the porcupine), Hathi's sons, the Bandar-log (the monkeys; a chaos test).
