---
description: "Record a constraining decision as an append-only ADR in decisions/."
argument-hint: "<slug> [the decision]"
---

The decision: **$ARGUMENTS**

You are recording a Cairn **decision** — a choice that constrains future work.

1. Find the next number `NNNN` by looking at existing files in `decisions/` (start at `0001`).
2. Write `decisions/NNNN-<slug>.md` from `${CLAUDE_PLUGIN_ROOT}/templates/decision.md`, filling in: Decision, Reason (the constraint it imposes), Alternatives (with why-not), Tradeoffs (what you knowingly give up). Set Date and Status: accepted.
3. Keep it to one page. Decisions are **append-only** — to change one, write a new record and mark the old one `superseded by NNNN`; never rewrite history.

Confirm the path written.
