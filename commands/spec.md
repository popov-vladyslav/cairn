---
description: "Turn a request into a spec (problem, goal, non-goals, acceptance criteria) saved to memory. No solution yet."
argument-hint: "<feature-slug> [the request]"
---

The feature and request: **$ARGUMENTS**

You are running the **Spec** stage of the Cairn loop. Specs live in the **central memory store**, not this repo.

**Resolve paths.** `<project>` = this repo's folder name (e.g. `renderers`). `<memory>` = the `Memory:` path in this repo's `AGENTS.md` if set; otherwise default to `${CLAUDE_PLUGIN_ROOT}/memory` (the cairn repo's store — if Cairn runs from a copied plugin rather than a directory source, set `Memory:` to your cairn repo's `memory/` so entries persist and version). The project's store is `<memory>/<project>/`.

1. **Scaffold.** Ensure `<memory>/<project>/{specs,decisions,learnings}/` exist; create them if not. Then handle `AGENTS.md` at the repo root:
   - none → copy `${CLAUDE_PLUGIN_ROOT}/templates/AGENTS.md` and fill its `Memory:` line with the absolute `<memory>/<project>` path.
   - exists, no Cairn → append the loop + rules from that template under a `## Cairn` heading (include the `Memory:` line); don't overwrite.
   - already Cairn → leave it untouched.
2. **Rules and recall first.** If `<memory>/<project>/rules.md` exists, read it — its rules constrain the spec (cite a rule slug where one bears on a criterion or non-goal). Then, before drafting, search the memory store for prior work — `/cairn:recall` over `<memory>` (this project first, then others) on the tags/keywords you infer from the request. Summarize any prior spec/decision/learning you'll reuse, and link it under Constraints / links.
3. **Draft** from `${CLAUDE_PLUGIN_ROOT}/templates/spec.md`: capture the request verbatim at the top; fill Problem, Goal, Non-goals, Acceptance criteria (observable, individually testable; prefix a criterion with its scope, e.g. `(mobile)`, when the project's `Verify:` line distinguishes scopes), and the frontmatter (`project`, `type: spec`, `slug`, `tags`, `refs`, `scope`, `status: draft`, `date`). Delete any section that adds no information. **Write no solution and no plan** — that comes later.
   - `tags:` — at most **5**. Platforms and areas (mobile, web, server, infra) go in `scope:`, not in tags.
   - `scope:` — the platforms/areas the change touches, e.g. `[mobile, web]`.
   - `refs:` — external references (issue numbers, tracker task ids, links). The plugin does not know which tracker you use; record whatever the owner names, or leave `[]`.
   - **Size cap.** If the drafted spec exceeds ~1,500 words, split it into phases: one spec per phase, slugs `<feature-slug>-phase-N`, each with its own acceptance criteria (and its own Challenge later). Link the phases to each other under Constraints / links.
4. If the request is too vague to state testable acceptance criteria, ask **one** sharp clarifying question instead of guessing.
5. **Approval gate.** Show the drafted spec(s) + proposed tags and ask with AskUserQuestion to confirm it's accurate and the tags are right. Only on approval, write it to `<memory>/<project>/specs/<feature-slug>.md` (or one file per phase).

End by showing the saved spec path(s) and instructing: run `/cairn:challenge <feature-slug>` next.
