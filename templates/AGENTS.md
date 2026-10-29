# AGENTS.md — how to work in this repo (Cairn)

You are a coding agent. Read this file, then the relevant spec and decisions in the
Cairn **memory store** (see `## Memory`) before touching code. Follow the loop. Don't skip Challenge.

Memory: `<absolute path to <cairn>/memory/<project> — filled by /cairn:spec>`
Mode: teach-first (teach-first | agent-implements)
Git: agent (agent | owner — who commits code)
Gates: (optional; one per line: "<step kind> -> <condition that must be confirmed before such a step starts>")
Verify: (optional; one per line: "<criterion scope> -> <evidence that proves it>")
Models: (optional; one per line: "<agent> -> <model>", e.g. "implementer -> opus")

## Loop
Request -> Spec -> Challenge -> Plan -> Implement -> Review -> Complete
- Never write code before the spec's acceptance criteria exist.
- Never plan before the Challenge gate is resolved.
- Implement one plan step per change; keep the build green after each.

Commands (Claude Code): /cairn:spec  /cairn:challenge  /cairn:plan  /cairn:next  /cairn:review  /cairn:decide

## Delegation & coding rules
Project settings: the `Mode:`, `Git:`, `Gates:`, `Verify:` and `Models:` lines under `Memory:` above override the defaults below. `Mode: agent-implements` lets the agent implement plan steps directly; `Git: owner` means the agent never commits (it lists changed files + a suggested message); `Gates:` names step kinds that need a confirmed condition before they start; `Verify:` names the evidence that proves each criterion scope at Review; `Models:` overrides a subagent's model (one per line, "<agent> -> <model>", e.g. "implementer -> opus"; see Model routing). An absent key means the default (teach-first, agent commits, no gates, evidence = test / behaviour / file, the Model routing defaults).
- Teach first: default output is an instruction (what / where / why + copy-paste example), not a commit. You implement; the agent reviews.
- Small pieces only. Split into the smallest green steps.
- Obvious small piece -> ask (AskUserQuestion); only after approval may a subagent write it.
- Subagents get a strict, minimal brief: one task, exact files, acceptance check, only the needed context.
- Subagents own their slice's quality; the main agent validates every output and owns the full picture.
- Delegate mainly at Implement (one subagent per step) and Review (a fresh, no-context reviewer).
- Route models per Model routing below: the implementer subagent runs on sonnet, the reviewer on opus, review-failed rework and `[large]` steps on opus; `Models:` overrides per project.
- Reuse existing patterns, libraries, and utilities before adding new ones.
- Match the surrounding style; don't reformat unrelated code.
- No new dependency without a one-line justification (and a decision record if it's load-bearing).
- Smallest change that satisfies the acceptance criteria. No speculative abstraction.
- Leave the build green: typecheck, lint, and tests pass before you stop.

## Model routing
| Work | Who | Model |
|---|---|---|
| Spec / Challenge / Plan authoring, talking to the user, memory writes | main agent | session model (run the session on opus for best results) |
| Implement a plan step from an approved plan (patch, tests) | `cairn-implementer` | sonnet |
| Review (fresh, no-context) | `cairn-reviewer` | opus |

- **Review-failed rework runs on opus.** When `/cairn:next` re-implements a step because `/cairn:review` returned a fix list for it, spawn `cairn-implementer` with the Agent tool's `model: opus` override instead of retrying on sonnet.
- **Large blast-radius steps run on opus.** When the plan step being implemented is marked `[large]` (Large per the Blast-radius rules: shared utils, data shape, public API, auth, payments, migrations, deletes), `/cairn:next` spawns `cairn-implementer` with the Agent tool's `model: opus` override up front, not after a failed review. Small/medium steps stay on sonnet.
- **Per-project override.** A `Models:` entry in the project's `AGENTS.md` (`"implementer -> <model>"` read by `/cairn:next`, `"reviewer -> <model>"` read by `/cairn:review`) is passed as the Agent tool's `model` override. Accepted values: `sonnet`, `opus`, `haiku`, `fable`. An entry wins over the two opus rules above, which only replace the sonnet default. An absent key means the defaults above.

## Review rules
- Re-read the spec's acceptance criteria; check the diff against each, one by one.
- Confirm no rule above was violated and the blast radius matches the plan.
- State clearly: PASS (Complete) or the specific fixes still needed.

## Confidence rules
- Before acting on anything ambiguous, state confidence: High / Medium / Low.
- Medium or Low on anything hard to revert or wide-reaching -> STOP and ask.
- Prefer one good question over a wrong assumption. Don't guess silently.

## Blast-radius rules
- Small  = one file / local effect -> proceed, small steps.
- Medium = a module / several files -> plan first, list affected files.
- Large  = shared utils, data shape, public API, auth, payments, migrations, deletes
           -> mandatory Challenge + decision record + explicit human confirmation before Implement.
- Bigger blast radius => more challenge, smaller steps, slower commits.

## Memory (durable — central)
- Specs, decisions, learnings live in the Cairn memory store at the `Memory:` path above, under this project's folder (`specs/`, `specs/<slug>.plan.md`, `decisions/`, `learnings/`, plus an optional `rules.md` of standing project rules). This repo stays clean.
- Every entry is tagged (frontmatter: project, type, slug, tags (max 5), status, date; specs add refs and scope). Reuse tags before adding new ones.
- Spec status: draft -> active (plan written) -> complete (Review PASS) | superseded. Only /cairn:spec, /cairn:plan and /cairn:review move it.
- Recall is automatic at Spec/Challenge (search memory first). Writes are approval-gated (AskUserQuestion confirms each entry).
- Distill a learning at Complete. Plans are disposable; specs/decisions/learnings are durable.
