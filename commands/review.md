---
description: "Verify the change against the spec's acceptance criteria and the Cairn rules. PASS = Complete, else a fix list."
argument-hint: "<feature-slug>"
---

The feature: **$ARGUMENTS**

You are running the **Review** stage of the Cairn loop. Review the whole change as an independent reviewer would — re-read the artifacts, don't rely on memory of having written the code.

1. Read `specs/<feature-slug>.md`, its plan (the `## Plan` section or the sibling `specs/<feature-slug>.plan.md`), and the diff for this feature.
2. Check the diff against **each acceptance criterion**, one by one. Mark each met / not met with the evidence (test, behavior, file).
3. Check the Cairn rules: no unjustified new dependency, existing patterns reused, no unrelated reformatting, build green, blast radius matches the plan.
4. Verdict:
   - **PASS → Complete.** Prune the disposable plan: delete the `## Plan` section from `specs/<feature-slug>.md`, or delete the sibling `specs/<feature-slug>.plan.md` — whichever holds it. Leave the durable spec (request, problem, goal, non-goals, acceptance criteria, `## Challenge`) intact. If any choice now constrains future work, run `/cairn:decide <slug>` to record it.
   - **Otherwise**, list the specific fixes needed and point back to `/cairn:next`.

Be concrete. "Seems right" is not a pass — name the evidence for each criterion.
