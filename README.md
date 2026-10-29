# Cairn

A lightweight, AI-native build loop for coding agents, packaged as a Claude Code plugin.

```
Request → Spec → Challenge → Plan → Implement → Review → Complete
```

Specs, decisions, and learnings are **durable memory**; plans are **disposable**. Memory is **central** — kept in your Cairn clone under `memory/<project>/` and tagged — so prior approaches get reused across projects instead of reinvented. `memory/<project>/` folders are gitignored: this repo ships the loop, and your specs stay on your machine. The one gate that's never skipped is **Challenge** — the agent interrogates the request *before* planning, looking for hidden assumptions, simpler or already-existing solutions, and unnecessary complexity, so it solves the underlying problem instead of blindly building the literal ask.

Cairn is **teach-first**: by default the agent hands you a precise instruction (what / where / why + example) and reviews what you build; it writes code itself only for obvious small pieces you approve, delegating those to the `cairn-implementer` agent (sonnet) and independent review to the `cairn-reviewer` agent (opus), each with a strict, minimal brief.

## Install

**From GitHub (recommended):**
```text
/plugin marketplace add popov-vladyslav/cairn
/plugin install cairn@cairn
```
Then set the `Memory:` line in each project's `AGENTS.md` to a `memory/<project>` folder in a repo you control (an installed plugin's own folder is a throwaway copy — see `CONTRIBUTING.md`).

**Option A — local checkout, no marketplace:**
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
| `/cairn:next <slug>` | Implement | Briefs the next unchecked step (teach-first) or implements it (`Mode: agent-implements`); obvious small pieces go to the `cairn-implementer` agent (sonnet) on approval. Confirms any `[gate: …]` first. Keeps the build green, ticks the box, commits (or lists the change if `Git: owner`), stops. |
| `/cairn:review <slug>` | Review | The fresh, no-context `cairn-reviewer` agent (opus) checks the diff against each acceptance criterion (with the evidence `Verify:` demands) + the rules. PASS = ticks criteria, `status: complete`, `## Outcome`, plan pruned; else a fix list. |
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
specs/<slug>.md          # durable: request, problem, goal, non-goals, acceptance (+ Challenge, + inline Plan for small/medium work, + Outcome at PASS)
specs/<slug>.plan.md     # disposable: the plan for large work (pruned at Complete)
decisions/NNNN-*.md      # durable, append-only decision records
learnings/<slug>.md      # distilled, reusable lessons from completed work
rules.md                 # optional: standing project rules, one bullet per slug (read by spec/challenge/next/review/recall)
```

Every entry is tagged; `/cairn:recall` and the automatic recall in Spec/Challenge use those tags to surface prior work.

### Project settings

Five optional lines directly under `Memory:` in the project's `AGENTS.md` tune the loop per project. Absent keys mean the defaults, which are today's behaviour.

| Key | Values | Default | Effect |
|---|---|---|---|
| `Mode:` | `teach-first` \| `agent-implements` | `teach-first` | `agent-implements` makes `/cairn:next` implement steps directly instead of briefing you. |
| `Git:` | `agent` \| `owner` | `agent` | `owner` means the agent never commits — `next`, `learn`, `decide`, `review` list changes and leave the commit to you. |
| `Gates:` | one per line: `"<step kind> -> <condition>"` | none | `/cairn:plan` marks matching steps `[gate: <name>]`; `/cairn:next` asks for the condition before starting one. |
| `Verify:` | one per line: `"<criterion scope> -> <evidence>"` | none | `/cairn:review` requires that evidence for criteria with a matching `(scope)` prefix; otherwise test / behaviour / file. |
| `Models:` | one per line: `"<agent> -> <model>"`, e.g. `"implementer -> opus"` | the Model routing defaults | `/cairn:next` passes the `implementer` entry and `/cairn:review` the `reviewer` entry as the subagent's model override. Accepted values: `sonnet`, `opus`, `haiku`, `fable`. An entry wins over the opus rules for rework and `[large]` steps. |

## Model routing

Opus thinks, Sonnet types. The plugin ships two agents (`agents/`), each with a fixed model, so routing holds whatever model your session runs on.

| Work | Who | Model |
|---|---|---|
| Spec / Challenge / Plan authoring, talking to you, memory writes | main agent | your session model (run the session on opus for best results) |
| Implement a plan step from an approved plan (patch, tests) | `cairn-implementer` | sonnet |
| Review (fresh, no-context) | `cairn-reviewer` | opus |

- **Review-failed rework runs on opus.** When `/cairn:next` re-implements a step because `/cairn:review` returned a fix list for it, the implementer is spawned on opus instead of retrying on sonnet.
- **Large blast-radius steps run on opus.** `/cairn:plan` marks Large blast-radius steps (shared utils, data shape, public API, auth, payments, migrations, deletes) `[large]`, and `/cairn:next` spawns the implementer on opus for them up front, not after a failed review. Small/medium steps stay on sonnet.
- **Override per project** with a `Models:` line under `Memory:` in the project's `AGENTS.md`, one entry per line:
  ```text
  Models: implementer -> opus
  ```
  Accepted values are `sonnet`, `opus`, `haiku` and `fable` (the aliases the Agent tool's `model` parameter takes). An entry wins over the two opus rules, which only replace the sonnet default, so `implementer -> fable` is never downgraded. Absent means the defaults above. Claude Code lists the agents as `cairn:cairn-implementer` and `cairn:cairn-reviewer` (see `/agents`).

**Session model.** The main agent runs on whatever model your session uses — e.g. Opus day to day, Fable 5.1 for important work. Subagent models are fixed by the agents' frontmatter and are not affected by the session model; apart from the two opus rules above, only the `Models:` key in `AGENTS.md` overrides them.

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
