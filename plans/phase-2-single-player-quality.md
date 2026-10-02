# Phase 2 — Single-player quality

[Overarching Android roadmap](android-roadmap.md) · [Previous: Phase 1](phase-1-competition.md) · [Next: Phase 3](phase-3-co-located-multiplayer.md)

Status: accepted phase outcome; specific improvements and detailed execution plan await owner selection. This outline does not authorize implementation.

## Intended outcome

A polished, reliable, enjoyable offline-first casual cooking game, building on the recovered baseline and the optional competition feature.

## What this phase should handle

Select and detail improvements to tutorial usability, save continuity/compatibility, map/device support, and difficulty. Review-derived candidates include:

- An action-gated tutorial that teaches through verified player actions.
- Saving basket selection, ingredient-fetch progress, and scoring streak continuity where approved.
- A versioned save boundary with stable map/recipe identities and an explicit legacy migration/recovery policy.
- Less fragile recipe catalogue ownership and compatibility when authored content changes.
- Map/layout behavior across selected device dimensions and evidence-backed rendering improvements.
- Difficulty/balance refinements chosen by the owner, with gameplay verification.

These are candidates, not a commitment to implement every proposed design in the review.

## Dependencies and planning gate

Use Phase 0 limitations and Phase 1 changes as the baseline. Recheck the [repository review](../docs/reports/2026-09-21-repository-review.md), select quality outcomes with the owner, and propose save migration and supported-device decisions before detailed tasks. Detail this plan when Phase 2 becomes the next phase; include bounded slices, acceptance criteria, tests/manual scenarios, and documentation owners.

## Boundaries and related ideas

Avoid introducing new economies, skins, power-ups, multiplayer, or iPhone support through quality work. Existing map/catalogue changes must respect save compatibility and synchronize runtime and authoring sources where necessary.

Related inspiration: [game quality and tutorial](../docs/ideas/inbox.md#game-quality-and-tutorial), [preference validation](../docs/ideas/inbox.md#preference-migration-and-account-validation), and [design/content concepts](../docs/ideas/inbox.md#design-content-and-release-concepts). Follow [accepted direction](../docs/direction.md) and [ADR 0002](../docs/decisions/0002-offline-first-competition-direction.md).
