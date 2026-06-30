---
description: "Mandatory gate: interrogate the spec before planning. Surface assumptions, simpler/existing solutions, risks. Don't agree by default."
argument-hint: "<feature-slug>"
---

The feature: **$ARGUMENTS**

You are running the **Challenge** stage of the Cairn loop. This gate is mandatory and must run before `/cairn:plan`. (`<memory>` = the `Memory:` path in this repo's `AGENTS.md`; `<project>` = the repo folder name.)

Read the spec at `<memory>/<project>/specs/<feature-slug>.md`, then **argue with the request on paper, where it's cheap.** Treat the proposed solution as a hypothesis, not an instruction. Investigate the actual codebase before answering — don't reason from assumptions.

Produce a `## Challenge` section appended to the spec, covering:

- **Hidden assumptions** — what the request takes for granted that may not hold.
- **Unclear requirements** — what's underspecified and needs a decision.
- **Existing solutions** — search the **memory store** (this project first, then others by tag, via `/cairn:recall`) and the repo for prior art: a pattern, a library, a prior decision/learning, or a feature flag that already does this or most of it. Prefer reusing an existing approach.
- **Simpler path** — the smaller-blast-radius way to get the same outcome (or to satisfy the *underlying problem* rather than the literal request).
- **Unnecessary complexity** — parts of the ask that can be cut or deferred.
- **Technical risks** — what could break, especially anything Large blast-radius (shared code, data shape, auth, migrations).

End with an explicit **Recommendation: proceed / revise / descope**, and a one-line reason.

If a high-stakes or hard-to-reverse point sits at Medium/Low confidence, ask the user **one** question before recommending.

## After the recommendation

The Challenge is **resolved** only once its `Recommendation:` line reads `proceed`. Until then the gate in `/cairn:plan` will refuse to plan. Act on the recommendation:

- **proceed** — the spec stands. Continue to `/cairn:plan <feature-slug>`.
- **revise** — edit the spec at `<memory>/<project>/specs/<feature-slug>.md` (problem, goal, non-goals, or acceptance criteria) to address the issues raised, then **re-run `/cairn:challenge <feature-slug>`**. Repeat until it lands on `proceed`.
- **descope** — narrow the acceptance criteria and move cut work into Non-goals, then **re-run `/cairn:challenge <feature-slug>`** on the smaller spec until it lands on `proceed`.

When you re-run, overwrite the previous `## Challenge` section rather than stacking a second one.

Next (once resolved): `/cairn:plan <feature-slug>`.
