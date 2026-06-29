# Contributing to Cairn

Cairn is a declarative Claude Code plugin: all behavior lives in markdown
(`commands/`, `skills/`, `templates/`) — there is no build step and no runtime code.

## Layout

- `commands/*.md` — the slash commands (`/cairn:spec`, `:challenge`, `:plan`, `:next`, `:review`, `:decide`). Each is a prompt with YAML frontmatter (`description`, `argument-hint`).
- `skills/rules/SKILL.md` — the operating manual that auto-loads in a Cairn repo.
- `templates/*.md` — scaffolding copied into user repos (`spec.md`, `plan.md`, `decision.md`, `AGENTS.md`).
- `.claude-plugin/` — `plugin.json` (the plugin manifest) and `marketplace.json` (single-plugin marketplace).

## Maintenance invariants

- **Keep `templates/AGENTS.md` and `skills/rules/SKILL.md` in sync.** They intentionally
  carry the same loop and the same coding / review / confidence / blast-radius rules — one for
  external agents (Codex, Gemini) via the copied `AGENTS.md`, one for Claude Code via the skill.
  When you change a rule, change it in **both**. Treat `SKILL.md` as the source of truth.
- **Keep the stage hand-offs consistent.** Every command ends by naming the next command, and
  several reference shared conventions that must agree across files:
  - A Challenge is *resolved* only when its `Recommendation:` line reads `proceed`
    (`challenge.md` defines it; `plan.md` gates on it).
  - The plan lives in the `## Plan` section of the spec (small/medium) **or** a sibling
    `specs/<slug>.plan.md` (large). `plan.md` writes it; `next.md` and `review.md` both read it;
    `review.md` prunes it. Change one, check all four.
- **Don't put maintainer notes in `templates/`** — those files are copied verbatim into user repos.

## Before publishing

- Run `claude plugin validate --strict` to catch manifest typos.
- Smoke-test locally with `claude --plugin-dir /absolute/path/to/cairn`, then run the loop end to end
  (`/cairn:spec` → `:challenge` → `:plan` → `:next` → `:review`) in a throwaway repo.
