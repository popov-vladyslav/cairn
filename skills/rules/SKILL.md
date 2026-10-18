---
name: rules
description: "The Cairn build loop and operating rules for this repo. Use when implementing a feature, fixing a non-trivial bug, planning a change, or whenever the repo has a specs/ or decisions/ directory or an AGENTS.md mentioning Cairn. Defines the Spec -> Challenge -> Plan -> Implement -> Review loop and the coding, review, confidence, and blast-radius rules every change must follow."
---

# Cairn — operating manual

You are a coding agent working under Cairn. Agents are stateless; the Cairn **memory store** is the durable memory (see `## Memory`). Read the relevant spec and any related decisions from memory before touching code. Follow the loop. **Do not skip Challenge.**

## The loop
`Request → Spec → Challenge → Plan → Implement → Review → Complete`

- **Spec** — turn the request into *what* and *why*: problem, goal, non-goals, acceptance criteria. No solution yet. (`/cairn:spec`)
- **Challenge** *(mandatory gate)* — interrogate the request before planning: hidden assumptions, unclear requirements, simpler or already-existing solutions, unnecessary complexity, technical risk. Do not agree by default. (`/cairn:challenge`)
- **Plan** — only after Challenge resolves: approach, affected files, ordered checkbox steps, risks, rollback. Disposable. (`/cairn:plan`)
- **Implement** — one plan step per change; keep the build green after each. (`/cairn:next`)
- **Review** — check the diff against the spec's acceptance criteria and these rules. (`/cairn:review`)
- **Complete** — criteria met. Record any decision worth keeping (`/cairn:decide`); prune the disposable plan.

Small work collapses several stages into a sentence each. The gate that is never skipped is **Challenge**.

## Delegation & coding rules
Cairn runs as an **orchestrator + subagents**. The main agent owns the full picture; subagents own slices.

**Project settings.** The `Mode:`, `Git:`, `Gates:` and `Verify:` lines directly under `Memory:` in the project's `AGENTS.md` override the defaults below. `Mode: agent-implements` lets the agent implement plan steps directly; `Git: owner` means the agent never commits (it lists changed files + a suggested message); `Gates:` names step kinds that need a confirmed condition before they start; `Verify:` names the evidence that proves each criterion scope at Review. An absent key means the default (teach-first, agent commits, no gates, evidence = test / behaviour / file). Every command that reads a key names it with exactly this spelling.

- **Teach first; don't code by default.** The main agent's normal output is an *instruction*, not a commit: what to change, where (file + location), why, and a copy-paste-ready example (a real snippet from this repo when one exists). You implement; the agent reviews. This keeps you in control and builds your understanding.
- **Small pieces only.** Never take a big job at once. Split into the smallest steps that each leave the build green and stand on their own.
- **Obvious small piece → ask, then delegate.** When a change is small and unambiguous, the agent asks with AskUserQuestion and, only once you approve, hands it to a subagent. (For these, skip the full teach-the-instruction detail.)
- **Subagents get a strict, minimal brief.** Each delegated task is transparent and self-contained: the one thing to do, the exact files, the acceptance check — and *only* the context that task needs. No repo-wide dumps, no unrelated goals.
- **Subagents own their slice; the main agent owns the whole.** A subagent is responsible for the correctness, quality, and build-greenness of its piece. The main agent validates each output against the brief and the spec, and is the only one that sees how the pieces fit.
- Delegation applies mainly at **Implement** (one subagent per plan step) and **Review** (a fresh subagent with no authoring context, for an honest check).
- Reuse existing patterns, libraries, and utilities before adding new ones.
- Match the surrounding style; do not reformat unrelated code.
- No new dependency without a one-line justification (and a decision record if it's load-bearing).
- Smallest change that satisfies the acceptance criteria. No speculative abstraction.
- Leave the build green: typecheck, lint, and tests pass before you stop.

## Review rules
- Re-read the spec's acceptance criteria; check the diff against each, one by one.
- Confirm no rule here was violated and the blast radius matches the plan.
- State clearly: PASS (Complete) or the specific fixes still needed.

## Confidence rules
- Before acting on anything ambiguous, state confidence: High / Medium / Low.
- Medium or Low on anything hard to revert or wide-reaching → STOP and ask one sharp question.
- Never guess silently.

## Blast-radius rules
- **Small** = one file / local effect → proceed, small steps.
- **Medium** = a module / several files → plan first, list affected files.
- **Large** = shared utils, data shape, public API, auth, payments, migrations, deletes → mandatory Challenge + decision record + explicit human confirmation before Implement.
- Bigger blast radius ⇒ more challenge, smaller steps, slower commits.

## Memory
Cairn's durable memory is **central**, not per-repo. Specs, decisions, and learnings live in the Cairn memory store, organized by project and tagged, so prior approaches are reused across projects.

```
<cairn>/memory/
  <project>/                 # e.g. renderers, zalando-app
    specs/<slug>.md          # durable: problem, goal, non-goals, acceptance, Challenge, Outcome
    specs/<slug>.plan.md     # disposable plan (large work); pruned at Complete
    decisions/NNNN-*.md      # durable, append-only ADRs
    learnings/<slug>.md      # distilled lessons from completed work
    rules.md                 # optional: standing project rules, one bullet per slug (read by spec/challenge/next/recall)
    INDEX.md                 # optional: tag -> entry map
```

- **Project** = the repo you're in (its folder name under `memory/`). The code repo stays clean — only its `AGENTS.md` carries the rules and the memory path.
- **Tags.** Every entry has frontmatter: `project`, `type` (spec|decision|learning|plan|reference|rules), `slug`, `tags: [..]` (at most 5), `status`, `date`. Specs also carry `refs: []` (external references) and `scope: []` (platforms/areas, which go here rather than in tags). Reuse existing tags before inventing new ones (`/cairn:recall` lists them).
- **Status lifecycle.** A spec is `draft` when written, `active` once its plan is written, `complete` at Review PASS (or `superseded`). Only `/cairn:spec`, `/cairn:plan` and `/cairn:review` move it.
- **Recall is automatic.** At Spec and Challenge the agent searches memory (this project first, then others) for prior specs/decisions/learnings on the same tags, and reuses the existing approach instead of reinventing it.
- **Writes are approval-gated.** Before saving anything to memory (spec, decision, learning), the agent shows the proposed entry + tags and asks with AskUserQuestion to confirm it's accurate and worth keeping. Nothing enters memory without your OK.
- **Learn at Complete.** When work finishes, distill what's reusable — the approach that worked, gotchas, patterns — into `learnings/` (`/cairn:learn`). That is how memory gets smarter per job.
- Plans are disposable; specs/decisions/learnings are durable. When implementation reveals the spec was wrong, update the spec in the same change. Chat deliberation and restated code aren't memory — only durable, reusable artifacts belong here.

The memory store lives in your Cairn clone (project folders under `memory/` are gitignored and stay local) and is found via the absolute `Memory:` path in each project's `AGENTS.md`.
