---
status: accepted
date: 2026-11-04
deciders: RN (owner)
---
# A short seeding report, and no DeepWiki-Open fallback

## Context and problem statement

Design doc 2 gives each seeding PR a five-part report as its description: the commits read, places on a fixed list where nothing was found, conflicts between docs and code, dependencies without a page, and what the skill couldn't work out. If reviewers rewrite more than half the statements, seeding switches to DeepWiki-Open ([0024](0024-seed-service-pages-with-a-claude-code-skill.md)). How much of this does the first system ([0069](0069-the-mvp-delivery-path-and-knowledge-library.md)) keep?

## Considered options

* A. A short report, and no fallback
* B. As designed
* C. No report

## Decision outcome

Chosen by the owner: A.

* **The seeding PR's description** gives the commit read, conflicts between docs and code, and what the skill couldn't work out.
* **The list of where to look** stays as guidance inside the skill, not something the report accounts for.
* **No DeepWiki-Open fallback.** If drafts are poor, we improve the skill, or the owners write the page.
* **Kept:** the read-only code reader, and the step where the person running it confirms the list of services.

### Consequences

* Good, because there's one way to seed, and no Docker, LiteLLM or Ollama setup waiting in reserve.
* Good, because reviewers still see the conflicts and gaps that tell them what to check.
* Bad, because if the skill can't produce usable drafts, owners write pages by hand until it's improved.
* Neutral: PRD §8, §9 and Q8 named DeepWiki-Open; applied to the PRD on the owner's choice.

## More information

Amends [0024](0024-seed-service-pages-with-a-claude-code-skill.md), dropping its fallback. Detail in [design/seeding.md](../design/seeding.md) and [plan.md, section 1](../plan.md#the-knowledge-library).

Amended by [0082](0082-seeding-skill-borrows-deepwiki-opens-method.md): the seeding PR also gives the files and line ranges behind each section.
