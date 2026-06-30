# Cairn memory store

Central, durable knowledge base for Cairn. Specs, decisions, and learnings live here — organized by project and tagged — so prior approaches get reused across projects. The code repos stay clean; only their `AGENTS.md` points here (via a `Memory:` line).

## Layout

```
memory/
  <project>/                 # repo folder name, e.g. renderers, zalando-app
    specs/<slug>.md          # durable: problem, goal, non-goals, acceptance, Challenge
    specs/<slug>.plan.md     # disposable plan (large work); pruned at Complete
    decisions/NNNN-*.md      # durable, append-only ADRs
    learnings/<slug>.md      # distilled lessons from completed work
    INDEX.md                 # optional: tag -> entry map
```

`/cairn:spec` creates a project's `specs/ decisions/ learnings/` on first run — you don't need to pre-create them.

## Frontmatter (every entry)

```yaml
---
project: renderers
type: spec | decision | learning
slug: dark-mode-toggle
tags: [theme, settings, persistence]
status: draft | active | accepted | complete | superseded
date: 2026-06-29
---
```

## Tagging

- Reuse existing tags before inventing new ones — run `/cairn:recall` to see what exists.
- Tags are the cross-project index: they're how `/cairn:recall` and the automatic recall in `/cairn:spec` / `/cairn:challenge` find prior approaches.

## How it's written and read

- **Read** (automatic): `/cairn:spec` and `/cairn:challenge` search memory before drafting/challenging.
- **Write** (approval-gated): `/cairn:spec`, `/cairn:decide`, and `/cairn:learn` each show the drafted entry + tags and ask before saving. Nothing lands here without approval.
- **Recall** (manual): `/cairn:recall <query | #tag> [project]`.

This store lives in the Cairn git repo and versions with it.
