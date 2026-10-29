---
description: "Verify the change against the spec's acceptance criteria and the Cairn rules. PASS = Complete, else a fix list."
argument-hint: "<feature-slug>"
---

The feature: **$ARGUMENTS**

You are running the **Review** stage of the Cairn loop. Review the whole change as an independent reviewer would — re-read the artifacts, don't rely on memory of having written the code. (`<memory>` = the `Memory:` path in this repo's `AGENTS.md`; `<project>` = the repo folder name.)

1. Read the spec at `<memory>/<project>/specs/<feature-slug>.md`, its plan (the `## Plan` section or the sibling `<memory>/<project>/specs/<feature-slug>.plan.md`), and the diff for this feature. From this repo's `AGENTS.md`, read the `Verify:` line(s) directly under `Memory:` (each `"<criterion scope> -> <evidence that proves it>"`), the `Git:` line, and the `Models:` line(s) (a `"reviewer -> <model>"` entry, if present, is passed as the Agent tool's `model` override; absent = the Model routing default, opus). If `<memory>/<project>/rules.md` exists, read it — its rules are part of what the reviewer checks.
2. **Delegate to a fresh reviewer.** Spawn the `cairn:cairn-reviewer` agent (**no authoring context**), given only: the spec's acceptance criteria, the `Verify:` entries, the diff, and the Cairn rules (plus `rules.md` if present). Ask it to check the diff against **each acceptance criterion** (met / not met, with evidence) and the rules (no unjustified new dependency, existing patterns reused, no unrelated reformatting, build green, blast radius matches the plan).
   - **Evidence per criterion.** A criterion may carry a scope prefix such as `(mobile)` or `(server)`. If `Verify:` has an entry for that scope, the evidence **must** be of the kind that entry names (e.g. a named test run with its exit code, a scenario run with its artifacts); a criterion with a `Verify:` entry but no such evidence is **not met**. Criteria with no matching `Verify:` entry — or when there is no `Verify:` line at all — are proven by test / behaviour / file, as before.
   - You validate its verdict and are accountable for it.
3. Verdict:
   - **PASS → Complete.** In this order:
     1. Tick every acceptance criterion in the spec: `- [ ]` → `- [x]`.
     2. Set the spec's frontmatter `status: complete`.
     3. Append a `## Outcome` section to the spec: date, `PASS`, the gates run (typecheck / lint / tests / `Verify:` runs) with their exit codes or results, follow-ups, open nits.
     4. Prune the disposable plan: delete the `## Plan` section from the spec, or delete the sibling `<memory>/<project>/specs/<feature-slug>.plan.md` — whichever holds it. Leave the durable spec (request, problem, goal, non-goals, acceptance criteria, `## Challenge`, `## Outcome`) intact.
     5. List the spec's `refs:` entries (external issues / tracker items) as **"close externally"** — the plugin does not close them.
     6. If `Git:` is `owner`, remind the owner to commit `memory/`.
     Then run `/cairn:learn <slug>` to capture what's reusable, and if any choice now constrains future work, `/cairn:decide <slug>`.
   - **Otherwise**, list the specific fixes needed and point back to `/cairn:next`. The fix list is rework: if `cairn:cairn-implementer` does it, it runs on opus unless `Models:` sets the implementer (per Model routing).

Be concrete. "Seems right" is not a pass — name the evidence for each criterion.
