# KnowledgeX enhancements that would help Bageera

_Each item is framed as a general KnowledgeX feature, useful beyond Bageera. Checked against KnowledgeX v0.5.0 source on 2026-10-24._

Bageera uses KnowledgeX in two ways: `kx check` / `kx review` validate the wiki (`council-rock`) as an OKF bundle ([ADR 0003](decisions/0003-wiki-page-format-okf.md)), and the KnowledgeX MCP server is the long-term option for trust- and freshness-aware wiki search ([ADR 0006](decisions/0006-wiki-read-access-for-agents.md), option D). Today both need workarounds. These changes remove them.

## Summary

| # | Feature | General use | What it removes in Bageera | Priority |
| --- | --- | --- | --- | --- |
| 1 | Read-only mode | Shared team notebooks, read-only mounts, CI | Unlocks KnowledgeX MCP as the wiki reader | High |
| 2 | Ignore non-note files | Any bundle that lives in a git repo with a README, CLAUDE.md, CHANGELOG | The `kx-check.sh` filtering wrapper | High |
| 3 | JSON output and CI exit codes | CI, editors, dashboards | Text parsing in lint; wiki-health numbers for `chil` | High |
| 4 | Orphan notes in `kx review` | Every notebook | `orphans.sh` | Medium |
| 5 | Source freshness from git | Notes that describe code or documents in git | `stale-services.sh` | Medium |
| 6 | Bundle-defined types | Domain vocabularies (services, runbooks, …) | Mapping service/feature pages onto `Entity` | Low |

Items 1–3 are worth doing before Phase 1 builds its wrappers; 4–6 can follow.

## 1. Read-only mode

**Today.** The MCP server exposes all nine tools, including `save_note`, `update_note` and `link_notes`. Opening a library also writes: `openLibrary` rebuilds the library `index.md` and records fingerprints in `.knowledgex.json`, and a detected user edit appends to `log.md` and rebuilds the notebook index. So even a "read" session changes files.

**Feature.** `KX_READONLY=1` (and `kx mcp --read-only`):
* register only the read tools (`read_guide`, `search_notes`, `read_note`, `check_up`, `list_notebooks`);
* never write any file; compute trust and fingerprints in memory only;
* say in `read_guide` output that the connection is read-only, so the agent doesn't offer to save.

**Why it's general.** Lets a team share one notebook folder (network drive, git checkout, synced folder) where only some people or agents may write, and lets CI read a bundle without dirtying the checkout.

**For Bageera.** The plugin could point the KnowledgeX MCP server at the shared local wiki copy, giving agents search results that say "unverified" or "stale since …" instead of GitHub's plain keyword search.

## 2. Ignore non-note files

**Today.** Every `.md` file except `index.md` and `log.md` is a note. A bundle at a repo root with `README.md`, `CLAUDE.md` or `CHANGELOG.md` gets "missing front-matter" errors, so `kx check` fails CI.

**Feature.** A `.okfignore` file at the bundle root, gitignore syntax, honoured by loading, `check`, `review`, `index` and search. Sensible defaults even without the file: `README.md`, `CLAUDE.md`, `AGENTS.md`, `CHANGELOG.md`, `CONTRIBUTING.md`, `LICENSE*`, `.github/`.

**Why it's general.** Most bundles kept in git have these files. It's also a clean way to skip folders such as templates or drafts.

**For Bageera.** Drops the filtering wrapper; lets `.templates/` be a normal folder instead of a hidden one.

## 3. JSON output and CI exit codes

**Today.** `kx check` and `kx review` print text; `check` exits non-zero on errors.

**Feature.** `--json` on `check`, `review` and `search` (stable shape: `{file, level, code, message}` for findings; one object per group for review), plus `--fail-on warning|error` for `check`. Give each finding a stable `code` (`broken-link`, `missing-description`, `unknown-key`, …).

**Why it's general.** Lets CI annotate PR lines, editors show problems inline, and anyone chart notebook health over time.

**For Bageera.** The wiki-checks workflow posts findings as PR annotations; the nightly lint feeds counts to the wiki-health dashboard in `chil`.

## 4. Orphan notes in `kx review`

**Today.** `review` lists stale, unconfirmed, conflicting and draft notes, but not notes nothing links to. Every note appears in the generated `index.md`, so "linked from somewhere" is always true.

**Feature.** A "Not linked from any note" group in `review` (links from `index.md` and `log.md` don't count). Optionally `Timeline` and `Source` notes are exempt, since they're often leaves.

**Why it's general.** Orphans are the usual sign that a note was saved and forgotten, or that a link was lost in a rename.

**For Bageera.** One of the four lint checks the architecture names comes for free.

## 5. Source freshness from git

**Today.** `review` already has "Sources changed since last verified", but it only compares a source's `last_modified` field, which someone must fill in by hand.

**Feature.** `kx review --refresh-sources` (opt-in, since it reads outside the bundle): for a source whose `resource` is a local git path or a GitHub URL (optionally with a path), look up the latest commit date and use it as `last_modified` for the comparison, without writing the note. Cache results per run.

**Why it's general.** Any note that describes code, a config repo or a document kept in git ("how the billing service retries") goes quietly stale when that thing changes. This catches it.

**For Bageera.** "Service code changed but the page didn't" becomes a built-in review group: each service page lists its repo in `sources`, and nightly lint calls `kx review --refresh-sources --json`.

## 6. Bundle-defined types

**Today.** Types outside the 12-type vocabulary get a warning, and only built-in types get a default `stale_after`.

**Feature.** A bundle-level profile (for example `.okf/profile.yaml`) that adds types with their stale windows and one-line definitions, which `read_guide` and `check` then respect.

**Why it's general.** Teams keep domain-specific notes (runbooks, services, vendors, experiments) and want them typed and expiring sensibly.

**For Bageera.** Wiki pages could say `type: Service` and `type: Feature` instead of reusing `Entity`. Low priority: the mapping in ADR 0003 works without it.

## Not proposed

* **Contradiction detection by meaning** (two pages disagreeing in prose): needs a model; stays in Bageera's lint skill. KnowledgeX already handles *declared* contradictions (`contradicts`).
* **Remote bundles** (MCP reading straight from GitHub): Bageera keeps a local copy anyway, and this would add network code and auth to KnowledgeX.

## Next step

If you agree, each item becomes an issue in [rahulnyk/KnowledgeX](https://github.com/rahulnyk/KnowledgeX), worked on there as separate PRs. ADR 0003 and ADR 0006 would then drop their workarounds.
