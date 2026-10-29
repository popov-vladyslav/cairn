---
description: "Brief or implement the next unchecked plan step (per the project's Mode:), keep the build green, tick the box, commit (per Git:), and stop."
argument-hint: "<feature-slug>"
---

The feature: **$ARGUMENTS**

You are running one **Implement** step of the Cairn loop. Default mode is **teach-first**: you produce the instruction, the human implements, you review. (`<memory>` = the `Memory:` path in this repo's `AGENTS.md`; `<project>` = the repo folder name.)

0. **Read the project settings.** From this repo's `AGENTS.md`, read the lines directly under `Memory:` — `Mode:` (`teach-first` | `agent-implements`; default `teach-first`), `Git:` (`agent` | `owner`; default `agent`), `Gates:` (the gate conditions) and `Models:` (an `implementer -> <model>` entry, if present, is passed as the Agent tool's `model` override whenever you spawn `cairn:cairn-implementer`, and wins over the opus rules in step 4; absent = the Model routing default, sonnet). If `<memory>/<project>/rules.md` exists, read it and keep its rules in force for this step.
1. **Locate the plan** for `<feature-slug>`: the `## Plan` section in `<memory>/<project>/specs/<feature-slug>.md`, or the sibling `<memory>/<project>/specs/<feature-slug>.plan.md` if it exists. If neither exists, stop and tell the user to run `/cairn:plan <feature-slug>` first. Read only the plan's `### Steps` and the acceptance criteria the current step cites — not the whole spec.
2. Find the **first unchecked step**.
   - If none exist: say "all steps complete" and tell the user to run `/cairn:review <slug>`.
3. **Gate check.** If the step carries a `[gate: <name>]` marker, ask the user **once** with AskUserQuestion whether that gate's condition (as written in the `Gates:` line) is satisfied, and ask for the link or evidence. If it is not, stop without implementing and report what the gate is waiting on.
4. **Do the step, per `Mode:`.**
   - **`teach-first` (default) — brief the step.** Produce a tight instruction for that one step: what to change, where (file + location), why, and a copy-paste-ready example. Hand it to the user to implement, then review their result against the step. **Obvious small piece → delegate (only on approval):** if the step is small and unambiguous, ask with AskUserQuestion whether a subagent should do it instead. On approval, hand the **`cairn:cairn-implementer`** agent a strict, minimal brief — just that step's files and acceptance check, no extra context — and validate its output against the brief and the spec.
   - **`agent-implements` — implement the step directly**, yourself or via the `cairn:cairn-implementer` agent with a strict, minimal brief (that step's files and acceptance check only). Do not ask whether a subagent should do it. Validate the result against the step and the criteria it cites.
   - **Rework of a review-failed step** (a fix `/cairn:review` returned): if `cairn:cairn-implementer` does it (under either mode's rules above), spawn it with `model: opus` unless `Models:` sets the implementer (per Model routing).
   - **`[large]` step** (Large blast-radius, marked by `/cairn:plan`): if `cairn:cairn-implementer` does it (under either mode's rules above), spawn it with `model: opus` up front unless `Models:` sets the implementer; small/medium steps stay on sonnet (per Model routing).
5. **Keep the build green:** whoever implemented it, run typecheck, lint, and the relevant tests. Fix before stopping.
6. Tick the step's checkbox in the plan (in memory). If implementation revealed the spec was wrong, update the spec (in memory) in this same change.
7. **Commit, per `Git:`.**
   - **`agent` (default):** commit the code change **in this repo** with a short message referencing the feature and step. (If the repo doesn't use git, skip the commit and just report the change.)
   - **`owner`:** do **not** commit. List the changed files and a suggested commit message for the owner.
8. **Stop.** Report what changed and what the next step is. Do not run ahead to later steps unless the user says so.

If the step turns out to be Large blast-radius (shared code, data shape, auth, migrations) and wasn't flagged as such, pause and confirm with the user before proceeding.
