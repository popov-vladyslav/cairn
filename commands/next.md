---
description: "Brief the next unchecked plan step (teach-first), keep the build green, tick the box, commit, and stop."
argument-hint: "<feature-slug>"
---

The feature: **$ARGUMENTS**

You are running one **Implement** step of the Cairn loop. Default mode is **teach-first**: you produce the instruction, the human implements, you review. (`<memory>` = the `Memory:` path in this repo's `AGENTS.md`; `<project>` = the repo folder name.)

1. **Locate the plan** for `<feature-slug>`: the `## Plan` section in `<memory>/<project>/specs/<feature-slug>.md`, or the sibling `<memory>/<project>/specs/<feature-slug>.plan.md` if it exists. If neither exists, stop and tell the user to run `/cairn:plan <feature-slug>` first.
2. Find the **first unchecked step**.
   - If none exist: say "all steps complete" and tell the user to run `/cairn:review <slug>`.
3. **Brief the step (teach-first, default).** Produce a tight instruction for that one step: what to change, where (file + location), why, and a copy-paste-ready example. Hand it to the user to implement, then review their result against the step.
4. **Obvious small piece → delegate (only on approval).** If the step is small and unambiguous, ask with AskUserQuestion whether a subagent should do it instead. On approval, hand a **subagent** a strict, minimal brief — just that step's files and acceptance check, no extra context — and validate its output against the brief and the spec.
5. **Keep the build green:** whoever implemented it, run typecheck, lint, and the relevant tests. Fix before stopping.
6. Tick the step's checkbox in the plan (in memory). If implementation revealed the spec was wrong, update the spec (in memory) in this same change.
7. Commit the code change **in this repo** with a short message referencing the feature and step. (If the repo doesn't use git, skip the commit and just report the change.)
8. **Stop.** Report what changed and what the next step is. Do not run ahead to later steps unless the user says so.

If the step turns out to be Large blast-radius (shared code, data shape, auth, migrations) and wasn't flagged as such, pause and confirm with the user before proceeding.
