# Cairn memory store

Central, durable knowledge base for Cairn. Specs, decisions, and learnings live here — organized by project and tagged — so prior approaches get reused across projects. The code repos stay clean; only their `AGENTS.md` points here (via a `Memory:` line).

## Layout

```
memory/
  <project>/                 # repo folder name, e.g. my-app, my-api
    specs/<slug>.md          # durable: problem, goal, non-goals, acceptance, Challenge, Outcome
    specs/<slug>.plan.md     # disposable plan (large work; type: plan); pruned at Complete
    decisions/NNNN-*.md      # durable, append-only ADRs
    learnings/<slug>.md      # distilled lessons from completed work
    rules.md                 # standing project rules, one bullet per slug (from templates/rules.md)
    INDEX.md                 # optional: tag -> entry map
```

`/cairn:spec` creates a project's `specs/ decisions/ learnings/` on first run — you don't need to pre-create them. `rules.md` is created by hand from `templates/rules.md` when a project accumulates standing rules; `spec`, `challenge`, `next`, `review` and `recall` read it when present.

## Frontmatter (every entry)

```yaml
---
project: my-app
type: spec | decision | learning | plan | reference | rules
slug: dark-mode-toggle
tags: [theme, settings, persistence]     # at most 5
refs: []                                 # specs only: external references (issues, tracker tasks, links)
scope: []                                # specs only: platforms / areas, e.g. [mobile, web, server, infra]
status: draft | active | accepted | complete | superseded
date: 2026-06-29
---
```

### Entry types

- **spec** — a unit of work with acceptance criteria. Lives in `specs/`.
- **plan** — the disposable plan for a large spec, as a sibling `specs/<slug>.plan.md`. Deleted at Review PASS.
- **decision** — an append-only ADR in `decisions/`. Changed only by a new record that supersedes it.
- **learning** — a distilled, reusable lesson in `learnings/`.
- **reference** — a living document with no acceptance criteria (e.g. a tech spec or architecture overview). Lives in `specs/`, stays `status: active`, and is never "completed".
- **rules** — the project's `rules.md`: one bullet per rule, `- **<slug>** — <rule> (<why>; source: <spec or decision slug>)`. Other entries cite rules as `[[slug]]`.

### `scope:` and `refs:`

- `scope:` holds the platforms/areas a spec touches. Acceptance criteria may carry a matching prefix, e.g. `- [ ] (mobile) …`; `/cairn:review` uses it to pick the evidence the project's `Verify:` line demands for that scope. Platforms go in `scope:`, **not** in `tags:`.
- `refs:` holds external references — issue numbers, tracker task ids, links. The plugin does not know which tracker a project uses; at Review PASS it lists them as "close externally".

### Status lifecycle (specs)

```
draft  →  active  →  complete
 (spec)   (plan written)  (Review PASS)        | superseded (replaced by another spec)
```

Only `/cairn:spec` (→ `draft`), `/cairn:plan` (→ `active`) and `/cairn:review` (→ `complete`) move a spec's status. At PASS, `review` also ticks the criteria, appends `## Outcome` (date, PASS, gates run, follow-ups, nits) and prunes the plan. Decisions are `accepted` (or `superseded`); learnings are `complete`; references and `rules.md` stay `active`.

## Tagging

- At most **5** tags per entry. Reuse existing tags before inventing new ones — run `/cairn:recall` to see what exists.
- Tags are the cross-project index: they're how `/cairn:recall` and the automatic recall in `/cairn:spec` / `/cairn:challenge` find prior approaches.

## How it's written and read

- **Read** (automatic): `/cairn:spec` and `/cairn:challenge` read `rules.md` and search memory before drafting/challenging; `/cairn:next` and `/cairn:review` read `rules.md` too.
- **Write** (approval-gated): `/cairn:spec`, `/cairn:decide`, and `/cairn:learn` each show the drafted entry + tags and ask before saving. Nothing lands here without approval.
- **Recall** (manual): `/cairn:recall <query | #tag> [project]` — matching rule slugs are printed first.

## Versioning and privacy

Project folders (`memory/<project>/`) are **gitignored by default**, so a public clone of Cairn never carries anyone's specs — only this README is tracked. To version your own memory, delete the `memory/*/` line from `.gitignore` in your clone, or point each project's `Memory:` line at a folder inside a private repo. Whether the agent commits memory writes itself is set per project by the `Git:` line in its `AGENTS.md` (`agent` commits; `owner` means the agent reminds the owner to commit `memory/`).
