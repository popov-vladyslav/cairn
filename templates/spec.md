---
project: <project>
type: spec
slug: <slug>
tags: []
refs: []
scope: []
status: draft
date: YYYY-MM-DD
---
# <Feature name>

> Request (verbatim): "<paste the user's actual ask>"

## Problem
What's wrong or missing today, and for whom. One short paragraph.

## Goal
What "good" looks like once this ships. Outcome, not implementation.

## Non-goals
What this explicitly does NOT do. Prevents scope creep and wrong assumptions.

## Acceptance criteria
- [ ] Observable, testable statement of done
- [ ] (scope) ...each one independently checkable; an optional scope prefix picks its Verify: evidence

## Verification
How each criterion is proven; default from the project's `Verify:` line in AGENTS.md (per scope), else test / behaviour / file.

## Constraints / links
Existing patterns to reuse, related specs/decisions, hard limits.

<!-- /cairn:challenge appends a ## Challenge section here, then /cairn:plan a ## Plan section (small/medium work) or a sibling <slug>.plan.md (large work) and sets status: active. At PASS, /cairn:review ticks the criteria, sets status: complete, appends a ## Outcome section (date, PASS, gates run, follow-ups, nits) and prunes the plan. -->
