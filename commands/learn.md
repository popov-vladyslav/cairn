---
description: "Distill a reusable learning from completed work into the memory store. Approval-gated."
argument-hint: "<slug> [what was learned]"
---

The learning: **$ARGUMENTS**

You are running `/cairn:learn` — capturing what's reusable from completed work so memory gets smarter per job. (`<memory>` = the `Memory:` path in this repo's `AGENTS.md`; `<project>` = the repo folder name.)

1. Draft `<slug>.md` from `${CLAUDE_PLUGIN_ROOT}/templates/learning.md`: a one-line lesson, **What worked** (the approach/pattern, linked to the spec + any decision), **Reuse** (when to reach for it again), **Gotchas**. Fill the frontmatter (`project`, `type: learning`, `slug`, `tags`, `status: complete`, `date`). Reuse existing tags before adding new ones — check with `/cairn:recall`.
2. **Approval gate.** Show the drafted learning + tags and ask with AskUserQuestion to confirm it's accurate and worth keeping. Only on approval, write `<memory>/<project>/learnings/<slug>.md`.
3. If `<memory>/<project>/INDEX.md` exists, add the new entry under its tags.

Confirm the path written.
