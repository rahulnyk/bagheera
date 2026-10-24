---
status: proposed
date: 2026-10-24
decision-makers: RN
---

# Agents read the wiki from a local clone, plus the official GitHub MCP server in read-only mode for cross-repo lookups

## Context and Problem Statement

The architecture exposes the wiki two ways: a git submodule or sparse checkout at `.wiki/` that skills read, and "a small read-only MCP server (an existing Karpathy-style `llm-wiki` MCP server, or `mcp-server-git`)". Which MCP server, and where does the checkout live?

## Decision Drivers

* Read-only must be enforced by the server, not by a prompt
* Prefer official or vendor-maintained servers (architecture, Layer 3)
* Self-host or keep data where it already is (architecture, Data)
* No per-repo churn across hundreds of service repos

## Considered Options

* MCP: A. official GitHub MCP server with read-only on and tools limited to `get_file_contents,search_code`; B. `mcp-server-git` (official reference server); C. community `llm-wiki` MCP servers (e.g. `flsteven87/llm-wiki-mcp`, `sumitroajiprabowo/llm-wiki-mcp`, `biks2013-tools/llm-wiki`); D. KnowledgeX MCP server over the local clone
* Checkout: 1. git submodule at `.wiki/` in every repo; 2. one shared sparse clone in the plugin's data directory, refreshed by the `SessionStart` hook

## Decision Outcome

Chosen options: "A" + "2", because the GitHub server has a server-side read-only mode, is already in the plugin's MCP list, and reads the wiki where it already lives; the shared clone gives skills fast grep access without touching service repos.

* Config (plugin `.mcp.json`): remote `https://api.githubcopilot.com/mcp/x/repos/readonly`, or the self-hosted image `ghcr.io/github/github-mcp-server --read-only --tools get_file_contents,search_code`. Skills pass `owner: seeonee, repo: council-rock`.
* Clone: `${CLAUDE_PLUGIN_DATA}/council-rock`, sparse (`org/ services/ decisions/ features/ index.md`; `raw/` excluded, see [0010](0010-curator-untrusted-input.md)), fetched at most hourly by `SessionStart` in the background.

### Consequences

* Good, because nothing new to host for the pilot, and access follows each engineer's existing GitHub permissions.
* Good, because skills read local files, which is fast and works offline.
* Bad, because GitHub code search is keyword-only and indexes with a delay; good enough while the wiki fits "grep plus an index file" (the architecture's own threshold).
* Bad, because the server can't be limited to one repo; skills name the repo, and the read-only flag means a wrong repo is a read, never a write.
* Bad, because this deviates slightly from the architecture's `.wiki/` path inside each repo. Skills get the path from the plugin, so moving it later is one change.

### Confirmation

Phase 1 test T1.9: from a session in a different repo, "what does the knowledgex service own?" is answered from `services/knowledgex.md` via the MCP server; a request to edit a wiki file through MCP fails because no write tool exists.

## Pros and Cons of the Options

### B. mcp-server-git

* Good, because official and local.
* Bad, because it has no read-only mode (it always exposes `git_add`, `git_commit`, `git_checkout`, …) and no tool to read a file at HEAD or search content; it answers "what changed", not "what does the wiki say".

### C. Community llm-wiki servers

* Good, because built for the Karpathy pattern.
* Bad, because every one we found is read-write by design, single-maintainer, and would run on 100 machines with the engineers' permissions (architecture, Governance: plugins run as the developer).

### D. KnowledgeX MCP

* Good, because trust- and freshness-aware search over exactly this page format ([0003](0003-wiki-page-format-okf.md)).
* Bad, because it has write tools and updates `index.md` and `.knowledgex.json` as a side effect of reads; it would need a read-only mode first. Worth revisiting once the wiki outgrows keyword search.

## More Information

Verify in Phase 1 whether the remote GitHub MCP endpoint needs a Copilot seat for your org; if it does, use the self-hosted image. Depends on GitHub.
