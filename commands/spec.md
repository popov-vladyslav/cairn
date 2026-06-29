---
description: "Turn a request into a spec (problem, goal, non-goals, acceptance criteria). No solution yet."
argument-hint: "<feature-slug> [the request]"
---

The feature and request: **$ARGUMENTS**

You are running the **Spec** stage of the Cairn loop.

1. **Scaffold if needed.** If `specs/` or `decisions/` don't exist, create them. For `AGENTS.md` at the repo root: if none exists, copy `${CLAUDE_PLUGIN_ROOT}/templates/AGENTS.md` so other agents (Codex, Gemini) inherit the same rules. If one exists but doesn't mention Cairn, append the loop + rules from that template under a `## Cairn` heading rather than overwriting it. If it already mentions Cairn, leave it untouched.
2. **Capture the request verbatim** at the top of the spec.
3. **Write `specs/<feature-slug>.md`** from `${CLAUDE_PLUGIN_ROOT}/templates/spec.md`. Fill in: Problem, Goal, Non-goals, Acceptance criteria (observable, individually testable). Delete any section that adds no information for this feature.
4. **Write no solution and no plan.** That comes later.
5. If the request is too vague to state testable acceptance criteria, ask **one** sharp clarifying question instead of guessing.

End by showing the spec and instructing: run `/cairn:challenge <feature-slug>` next.
