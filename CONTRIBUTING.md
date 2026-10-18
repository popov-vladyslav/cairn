# Contributing to Cairn

Cairn is a declarative Claude Code plugin: all behavior lives in markdown
(`commands/`, `skills/`, `templates/`) — there is no build step and no runtime code.

## Layout

- `commands/*.md` — the slash commands (`/cairn:spec`, `:challenge`, `:plan`, `:next`, `:review`, `:decide`, `:learn`, `:recall`). Each is a prompt with YAML frontmatter (`description`, `argument-hint`).
- `skills/rules/SKILL.md` — the operating manual that auto-loads in a Cairn repo.
- `templates/*.md` — scaffolding copied into user repos or memory (`spec.md`, `plan.md`, `decision.md`, `learning.md`, `rules.md`, `AGENTS.md`).
- `memory/` — the central knowledge store, one folder per project. `memory/README.md` is a maintainer doc; project content (specs, decisions, learnings) is written here at runtime by the commands.
- `.claude-plugin/` — `plugin.json` (the plugin manifest) and `marketplace.json` (single-plugin marketplace).

## Maintenance invariants

- **Keep `templates/AGENTS.md` and `skills/rules/SKILL.md` in sync.** They intentionally
  carry the same loop and the same `## Delegation & coding rules`, review, confidence, blast-radius,
  and `## Memory` sections — one for external agents (Codex, Gemini) via the copied `AGENTS.md`,
  one for Claude Code via the skill. When you change a rule, change it in **both**. Treat `SKILL.md`
  as the source of truth.
- **Keep the stage hand-offs consistent.** Every command ends by naming the next command, and
  several reference shared conventions that must agree across files:
  - A Challenge is *resolved* only when its `Recommendation:` line reads `proceed`
    (`challenge.md` defines it; `plan.md` gates on it).
  - The plan lives in the `## Plan` section of the spec (small/medium) **or** a sibling
    `<memory>/<project>/specs/<slug>.plan.md` (large). `plan.md` writes it; `next.md` and `review.md`
    both read it; `review.md` prunes it. Change one, check all four.
  - **Memory paths agree.** Every command resolves `<memory>` = the `Memory:` line in the repo's
    `AGENTS.md` (set by `/cairn:spec`) and `<project>` = the repo folder name. Keep that convention
    identical across `spec/challenge/plan/next/review/decide/learn/recall`.
  - **Writes are approval-gated; reads aren't.** `spec`, `decide`, and `learn` must AskUserQuestion
    before writing to memory; `recall` — and the automatic recall in `spec`/`challenge` — is read-only.
  - **Project settings keys are spelled identically everywhere.** The four keys under `Memory:` —
    `Mode:`, `Git:`, `Gates:`, `Verify:` — appear with exactly that spelling in `templates/AGENTS.md`,
    `skills/rules/SKILL.md` and every command that reads them (`next` reads `Mode:`/`Git:`/`Gates:`;
    `plan` reads `Gates:`; `review` reads `Verify:`/`Git:`; `learn` and `decide` read `Git:`). Absent
    keys must always mean the pre-settings default. Check with
    `grep -n "Mode:\|Git:\|Gates:\|Verify:" templates/AGENTS.md skills/rules/SKILL.md commands/*.md`.
  - **The status lifecycle is moved only by `spec`, `plan` and `review`.** `spec` writes `draft`,
    `plan` sets `active`, `review` sets `complete` (and ticks criteria, appends `## Outcome`, prunes
    the plan). No other command touches a spec's `status:`.
  - **`rules.md` is read, never auto-written.** `spec`, `challenge`, `next`, `review` and `recall` read
    `<memory>/<project>/rules.md` when present; it is created by hand from `templates/rules.md`.
- **Memory-path gotcha.** Memory is *data* and should live in a git repo you control so it versions and
  persists. `/cairn:spec` defaults `<memory>` to `${CLAUDE_PLUGIN_ROOT}/memory` — correct for a
  *directory-source* install (the plugin root **is** the repo), but a copied/GitHub install makes
  `${CLAUDE_PLUGIN_ROOT}` a throwaway copy. For those, set the `Memory:` line to your cairn clone's
  `memory/<project>` path.
- **Project memory stays local.** `.gitignore` ignores every `memory/<project>/` folder, so this repo
  never ships anyone's specs. Only `memory/README.md` is tracked. Never commit a `memory/<project>/`
  folder.
- **Don't put maintainer notes in `templates/`** — those files are copied verbatim into user repos.
  (`memory/README.md` is a maintainer doc and is *not* copied anywhere.)

## Before publishing

- Run `claude plugin validate --strict` to catch manifest typos.
- Smoke-test locally with `claude --plugin-dir /absolute/path/to/cairn`, then run the loop end to end
  (`/cairn:spec` → `:challenge` → `:plan` → `:next` → `:review` → `:learn`, with `/cairn:recall` to
  confirm the entry is found) in a throwaway repo, and confirm no memory write happens without an
  approval prompt.
