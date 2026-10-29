# Changelog

All notable changes to Cairn are documented here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and the project uses
[Semantic Versioning](https://semver.org/).

## 0.4.0 — 2026-10-29

### Added
- `agents/` with `cairn-implementer` (`model: sonnet`) and `cairn-reviewer` (`model: opus`), exposed as `cairn:cairn-implementer` and `cairn:cairn-reviewer`.
- `## Model routing` in `skills/rules/SKILL.md` and `templates/AGENTS.md`: main agent on the session model, implementer on sonnet, reviewer on opus, and review-failed rework and Large blast-radius steps on opus. A `Models:` entry wins over both opus rules.
- `Models:` project setting (optional, under `Memory:` in `AGENTS.md`; one `"<agent> -> <model>"` per line) to override a subagent's model per project. Accepted values: `sonnet`, `opus`, `haiku`, `fable`.
- `[large]` step marker: `/cairn:plan` marks Large blast-radius steps so `/cairn:next` can route them to opus without re-deriving the blast radius.

### Changed
- `/cairn:next` hands its brief to `cairn:cairn-implementer` and `/cairn:review` spawns `cairn:cairn-reviewer` instead of a generic subagent. No behaviour change other than model selection: same command prompts, approval gates, hand-offs and briefs. The agent files carry a role preamble that restates the existing delegation and review rules and the orchestrator's existing division of labour (the implementer does not commit, tick plan boxes or write memory; the reviewer writes nothing).
