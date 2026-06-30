# Cairn

A lightweight, AI-native build loop for coding agents, packaged as a Claude Code plugin.

```
Request → Spec → Challenge → Plan → Implement → Review → Complete
```

Specs, decisions, and learnings are **durable memory**; plans are **disposable**. Memory is **central** — kept in the Cairn repo under `memory/<project>/` and tagged — so prior approaches get reused across projects instead of reinvented. The one gate that's never skipped is **Challenge** — the agent interrogates the request *before* planning, looking for hidden assumptions, simpler or already-existing solutions, and unnecessary complexity, so it solves the underlying problem instead of blindly building the literal ask.

Cairn is **teach-first**: by default the agent hands you a precise instruction (what / where / why + example) and reviews what you build; it writes code itself only for obvious small pieces you approve, delegating those — and independent review — to subagents with strict, minimal briefs.

Distilled from [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) (phased lifecycle, slash commands, blast-radius/confidence discipline, reuse-don't-reinvent) and [genkovich/sdd](https://github.com/genkovich/sdd) (specs as artifacts each stage reads, gating, decision records, acceptance-criteria traceability) — minus the 24-skill / 16-stage weight. The **Challenge** gate is the addition neither has as a first-class step.

## Install (local)

**Option A — no marketplace, simplest:**
```bash
claude --plugin-dir /absolute/path/to/cairn
```

**Option B — local marketplace (install once, available across projects):**
```text
/plugin marketplace add /absolute/path/to/cairn
/plugin install cairn@cairn
```

Verify with `/plugin` or by typing `/cairn:` and seeing the commands. After editing a `SKILL.md`, changes apply immediately; after editing commands/manifest, run `/reload-plugins` or restart.

## Commands

| Command | Stage | What it does |
|---|---|---|
| `/cairn:spec <slug> [request]` | Spec | Request → problem, goal, non-goals, acceptance criteria, saved to memory. Scaffolds the project's memory store + `AGENTS.md` on first run, recalls prior work, approval-gated. No solution. |
| `/cairn:challenge <slug>` | Challenge | **Mandatory gate.** Interrogates the spec: assumptions, existing/simpler solutions (searches memory across projects), risks. Recommends proceed / revise / descope. |
| `/cairn:plan <slug>` | Plan | Disposable plan: approach, affected files, checkbox steps, risks, rollback. Refuses if Challenge is unresolved. |
| `/cairn:next <slug>` | Implement | Briefs the next unchecked step (teach-first); obvious small pieces go to a subagent on approval. Keeps the build green, ticks the box, commits, stops. |
| `/cairn:review <slug>` | Review | A fresh, no-context subagent checks the diff against each acceptance criterion + the rules. PASS = Complete, else a fix list. |
| `/cairn:learn <slug>` | Complete | Distills a reusable, tagged learning into memory. Approval-gated. |
| `/cairn:decide <slug>` | — | Records a constraining choice as an append-only ADR in memory. Approval-gated. |
| `/cairn:recall <query \| #tag>` | — | Searches memory (specs, decisions, learnings) by tag / project / keyword. Read-only. |

The `cairn` **skill** carries the operating manual (the loop + delegation/coding/review/confidence/blast-radius rules) and auto-loads when you work in a Cairn repo, so the rules apply even without typing a command.

## What it creates

In **your project** (kept clean):

```
AGENTS.md                # operating manual + a `Memory:` path line (also read by Codex / Gemini CLI)
```

In the **Cairn memory store** (`memory/<project>/`):

```
specs/<slug>.md          # durable: request, problem, goal, non-goals, acceptance (+ Challenge, + inline Plan for small/medium work)
specs/<slug>.plan.md     # disposable: the plan for large work (pruned at Complete)
decisions/NNNN-*.md      # durable, append-only decision records
learnings/<slug>.md      # distilled, reusable lessons from completed work
```

Every entry is tagged; `/cairn:recall` and the automatic recall in Spec/Challenge use those tags to surface prior work.

## Loop in 15 seconds

```text
/cairn:spec dark-mode "Add a dark mode toggle to Settings that remembers the choice"
/cairn:challenge dark-mode      # finds: do we even need persistence? reuse existing storage? no new lib.
/cairn:plan dark-mode
/cairn:next dark-mode           # repeat per step — briefed, you build (or approve a subagent), each green + committed
/cairn:review dark-mode         # fresh reviewer → PASS → Complete
/cairn:learn dark-mode          # capture what's reusable
/cairn:decide theme-via-context # record the constraining choice
```

MIT.
