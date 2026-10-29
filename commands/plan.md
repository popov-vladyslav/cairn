---
description: "Produce a disposable implementation plan (approach, affected files, checkbox steps, risks, rollback). Gated on the Challenge."
argument-hint: "<feature-slug>"
---

The feature: **$ARGUMENTS**

You are running the **Plan** stage of the Cairn loop. (`<memory>` = the `Memory:` path in this repo's `AGENTS.md`; `<project>` = the repo folder name.)

**Gate:** the spec at `<memory>/<project>/specs/<feature-slug>.md` must have a **resolved** `## Challenge` section — present, with its `Recommendation:` line reading `proceed`. If it's missing, or the recommendation is still `revise`/`descope`, stop and tell the user to run `/cairn:challenge <feature-slug>` first. Do not plan around an un-challenged spec.

Write the plan from `${CLAUDE_PLUGIN_ROOT}/templates/plan.md`. Choose **one** location by blast radius (see the Cairn rules) and use it consistently — `/cairn:next` and `/cairn:review` look in both places:

- **Small/medium blast radius** → a `## Plan` section appended to the spec at `<memory>/<project>/specs/<feature-slug>.md`.
- **Large blast radius** (shared utils, data shape, public API, auth, payments, migrations, deletes) → a sibling `<memory>/<project>/specs/<feature-slug>.plan.md`.

Include:

- **Approach** — the chosen path in 2–3 sentences, and why it beats the obvious alternative.
- **Affected files** — each path and what changes (mark new files).
- **Risks** — risk → mitigation.
- **Steps** — ordered checkboxes, each small enough to leave the build green on its own. Each step is an instruction you'll implement (teach-first); mark steps that are *obvious small pieces* a subagent could do on approval, and mark every Large blast-radius step (shared utils, data shape, public API, auth, payments, migrations, deletes) with `[large]` so `/cairn:next` sees it without re-deriving the blast radius.
- **Gates** — read the `Gates:` line(s) directly under `Memory:` in this repo's `AGENTS.md` (each `"<step kind> -> <condition>"`). Mark every step whose kind matches a gate with `[gate: <name>]`, where `<name>` is that gate's step kind; `/cairn:next` asks for the condition before starting such a step. No `Gates:` line → no markers.
- **Rollback** — how to undo cleanly; note any state left behind.

Keep it concise. The plan is disposable — it gets pruned at Complete.

When the plan is written, set the spec's frontmatter `status: active` (the plan is now the work in flight).

Next: `/cairn:next <feature-slug>` to implement step 1.
