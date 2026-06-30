---
description: "Search the memory store (specs, decisions, learnings) by tag, project, or keyword. Read-only."
argument-hint: "<query | #tag> [project]"
---

The query: **$ARGUMENTS**

You are running `/cairn:recall` — surfacing prior approaches so they get reused, not reinvented. Read-only; no approval needed. (`<memory>` = the `Memory:` path in this repo's `AGENTS.md`; `<project>` = the repo folder name.)

1. Search `<memory>` over entry frontmatter (`tags`, `type`, `slug`) and bodies. Default order: this project first, then other projects. If a project is named in `$ARGUMENTS`, scope to it; if a `#tag` is given, match that tag.
2. Return each match as: `project/type/slug` — its tags — a one-line summary. Group by relevance.
3. End with the distinct tag list found, so tags get reused rather than duplicated.

This is the engine the automatic recall in `/cairn:spec` and `/cairn:challenge` uses.
