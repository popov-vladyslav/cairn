---
description: "Implement only the next unchecked plan step, keep the build green, tick the box, commit, and stop."
argument-hint: "<feature-slug>"
---

The feature: **$ARGUMENTS**

You are running one **Implement** step of the Cairn loop.

1. **Locate the plan** for `<feature-slug>`: the `## Plan` section in `specs/<feature-slug>.md`, or the sibling `specs/<feature-slug>.plan.md` if it exists. If neither exists, stop and tell the user to run `/cairn:plan <feature-slug>` first.
2. Find the **first unchecked step**.
   - If none exist: say "all steps complete" and tell the user to run `/cairn:review <slug>`.
   - Otherwise, implement that one step.
3. Keep the build green: run typecheck, lint, and the relevant tests. If they don't pass, fix before stopping.
4. Tick the step's checkbox in the plan. If implementation revealed the spec was wrong, update the spec in this same change.
5. Commit with a short message referencing the feature and step. (If the repo doesn't use git, skip the commit and just report the change.)
6. **Stop.** Report what changed and what the next step is. Do not run ahead to later steps unless the user says so.

If the step turns out to be Large blast-radius (shared code, data shape, auth, migrations) and wasn't flagged as such, pause and confirm with the user before proceeding.
