# AGENTS.md — how to work in this repo (Cairn)

You are a coding agent. Read this file, then the relevant `specs/` and `decisions/`
before touching code. Follow the loop. Don't skip Challenge.

## Loop
Request -> Spec -> Challenge -> Plan -> Implement -> Review -> Complete
- Never write code before the spec's acceptance criteria exist.
- Never plan before the Challenge gate is resolved.
- Implement one plan step per change; keep the build green after each.

Commands (Claude Code): /cairn:spec  /cairn:challenge  /cairn:plan  /cairn:next  /cairn:review  /cairn:decide

## Coding rules
- Reuse existing patterns, libraries, and utilities before adding new ones.
- Match the surrounding style; don't reformat unrelated code.
- No new dependency without a one-line justification (and a decision record if it's load-bearing).
- Smallest change that satisfies the acceptance criteria. No speculative abstraction.
- Leave the build green: typecheck, lint, and tests pass before you stop.

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
