---
description: "Verify the change against the spec's acceptance criteria and the Cairn rules. PASS = Complete, else a fix list."
argument-hint: "<feature-slug>"
---

The feature: **$ARGUMENTS**

You are running the **Review** stage of the Cairn loop. Review the whole change as an independent reviewer would — re-read the artifacts, don't rely on memory of having written the code. (`<memory>` = the `Memory:` path in this repo's `AGENTS.md`; `<project>` = the repo folder name.)

1. Read the spec at `<memory>/<project>/specs/<feature-slug>.md`, its plan (the `## Plan` section or the sibling `<memory>/<project>/specs/<feature-slug>.plan.md`), and the diff for this feature.
2. **Delegate to a fresh reviewer.** Spawn a subagent with **no authoring context**, given only: the spec's acceptance criteria, the diff, and the Cairn rules. Ask it to check the diff against **each acceptance criterion** (met / not met, with evidence: test, behavior, file) and the rules (no unjustified new dependency, existing patterns reused, no unrelated reformatting, build green, blast radius matches the plan). You validate its verdict and are accountable for it.
3. Verdict:
   - **PASS → Complete.** Prune the disposable plan: delete the `## Plan` section from the spec, or delete the sibling `<memory>/<project>/specs/<feature-slug>.plan.md` — whichever holds it. Leave the durable spec (request, problem, goal, non-goals, acceptance criteria, `## Challenge`) intact. Then run `/cairn:learn <slug>` to capture what's reusable, and if any choice now constrains future work, `/cairn:decide <slug>`.
   - **Otherwise**, list the specific fixes needed and point back to `/cairn:next`.

Be concrete. "Seems right" is not a pass — name the evidence for each criterion.
