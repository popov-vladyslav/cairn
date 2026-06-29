---
description: "Produce a disposable implementation plan (approach, affected files, checkbox steps, risks, rollback). Gated on the Challenge."
argument-hint: "<feature-slug>"
---

The feature: **$ARGUMENTS**

You are running the **Plan** stage of the Cairn loop.

**Gate:** the spec must have a **resolved** `## Challenge` section — present, with its `Recommendation:` line reading `proceed`. If it's missing, or the recommendation is still `revise`/`descope`, stop and tell the user to run `/cairn:challenge <feature-slug>` first. Do not plan around an un-challenged spec.

Write the plan from `${CLAUDE_PLUGIN_ROOT}/templates/plan.md`. Choose **one** location by blast radius (see the Cairn rules) and use it consistently — `/cairn:next` and `/cairn:review` look in both places:

- **Small/medium blast radius** → a `## Plan` section appended to `specs/<feature-slug>.md`.
- **Large blast radius** (shared utils, data shape, public API, auth, payments, migrations, deletes) → a sibling `specs/<feature-slug>.plan.md`.

Include:

- **Approach** — the chosen path in 2–3 sentences, and why it beats the obvious alternative.
- **Affected files** — each path and what changes (mark new files).
- **Risks** — risk → mitigation.
- **Steps** — ordered checkboxes, each small enough to leave the build green on its own.
- **Rollback** — how to undo cleanly; note any state left behind.

Keep it concise. The plan is disposable — it gets pruned at Complete.

Next: `/cairn:next <feature-slug>` to implement step 1.
