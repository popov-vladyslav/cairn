---
name: rules
description: "The Cairn build loop and operating rules for this repo. Use when implementing a feature, fixing a non-trivial bug, planning a change, or whenever the repo has a specs/ or decisions/ directory or an AGENTS.md mentioning Cairn. Defines the Spec -> Challenge -> Plan -> Implement -> Review loop and the coding, review, confidence, and blast-radius rules every change must follow."
---

# Cairn — operating manual

You are a coding agent working under Cairn. Agents are stateless; the repo is the memory. Read the relevant `specs/<feature>.md` and any related `decisions/` before touching code. Follow the loop. **Do not skip Challenge.**

## The loop
`Request → Spec → Challenge → Plan → Implement → Review → Complete`

- **Spec** — turn the request into *what* and *why*: problem, goal, non-goals, acceptance criteria. No solution yet. (`/cairn:spec`)
- **Challenge** *(mandatory gate)* — interrogate the request before planning: hidden assumptions, unclear requirements, simpler or already-existing solutions, unnecessary complexity, technical risk. Do not agree by default. (`/cairn:challenge`)
- **Plan** — only after Challenge resolves: approach, affected files, ordered checkbox steps, risks, rollback. Disposable. (`/cairn:plan`)
- **Implement** — one plan step per change; keep the build green after each. (`/cairn:next`)
- **Review** — check the diff against the spec's acceptance criteria and these rules. (`/cairn:review`)
- **Complete** — criteria met. Record any decision worth keeping (`/cairn:decide`); prune the disposable plan.

Small work collapses several stages into a sentence each. The gate that is never skipped is **Challenge**.

## Coding rules
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
- `specs/` and `decisions/` are durable. Plans are disposable.
- When implementation reveals the spec was wrong, update the spec in the same change.
- When a choice constrains the future, write a decision (`/cairn:decide`).
- Chat deliberation and restated code are not memory — keep them out of the repo.
