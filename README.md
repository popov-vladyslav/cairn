# Cairn

A lightweight, AI-native build loop for coding agents, packaged as a Claude Code plugin.

```
Request → Spec → Challenge → Plan → Implement → Review → Complete
```

Specs and decisions are **durable repo memory**; plans are **disposable**. The one gate that's never skipped is **Challenge** — the agent interrogates the request *before* planning, looking for hidden assumptions, simpler or already-existing solutions, and unnecessary complexity, so it solves the underlying problem instead of blindly building the literal ask.

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
| `/cairn:spec <slug> [request]` | Spec | Request → problem, goal, non-goals, acceptance criteria. Scaffolds `specs/`, `decisions/`, `AGENTS.md` on first run. No solution. |
| `/cairn:challenge <slug>` | Challenge | **Mandatory gate.** Interrogates the spec: assumptions, existing/simpler solutions, risks. Recommends proceed / revise / descope. |
| `/cairn:plan <slug>` | Plan | Disposable plan: approach, affected files, checkbox steps, risks, rollback. Refuses if Challenge is unresolved. |
| `/cairn:next <slug>` | Implement | Implements only the next unchecked step, keeps the build green, ticks the box, commits, stops. |
| `/cairn:review <slug>` | Review | Checks the diff against each acceptance criterion + the rules. PASS = Complete, else a fix list. |
| `/cairn:decide <slug>` | — | Records a constraining choice as an append-only ADR in `decisions/`. |

The `cairn` **skill** carries the operating manual (the loop + coding/review/confidence/blast-radius rules) and auto-loads when you work in a Cairn repo, so the rules apply even without typing a command.

## What it creates in your project

```
AGENTS.md                # operating manual (also read by Codex / Gemini CLI)
specs/<feature>.md       # durable: request, problem, goal, non-goals, acceptance (+ Challenge, + inline Plan for small/medium work)
specs/<feature>.plan.md  # disposable: the plan for large work (pruned at Complete)
decisions/NNNN-*.md      # durable, append-only decision records
```

## Loop in 15 seconds

```text
/cairn:spec dark-mode "Add a dark mode toggle to Settings that remembers the choice"
/cairn:challenge dark-mode      # finds: do we even need persistence? reuse existing storage? no new lib.
/cairn:plan dark-mode
/cairn:next dark-mode           # repeat per step, each green + committed
/cairn:review dark-mode         # PASS → Complete
/cairn:decide theme-via-context # record the constraining choice
```

MIT.
