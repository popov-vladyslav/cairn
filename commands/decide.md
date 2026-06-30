---
description: "Record a constraining decision as an append-only ADR in the memory store."
argument-hint: "<slug> [the decision]"
---

The decision: **$ARGUMENTS**

You are recording a Cairn **decision** — a choice that constrains future work. Decisions live in the **memory store**. (`<memory>` = the `Memory:` path in this repo's `AGENTS.md`; `<project>` = the repo folder name.)

1. Find the next number `NNNN` by looking at existing files in `<memory>/<project>/decisions/` (start at `0001`).
2. Draft `NNNN-<slug>.md` from `${CLAUDE_PLUGIN_ROOT}/templates/decision.md`, filling in: the frontmatter (`project`, `type: decision`, `slug`, `tags`, `status: accepted`, `date`), Decision, Reason (the constraint it imposes), Alternatives (with why-not), Tradeoffs (what you knowingly give up).
3. Keep it to one page. Decisions are **append-only** — to change one, write a new record and mark the old one `superseded by NNNN`; never rewrite history.
4. **Approval gate.** Show the drafted record + tags and ask with AskUserQuestion to confirm before saving. Only on approval, write `<memory>/<project>/decisions/NNNN-<slug>.md`.

Confirm the path written.
