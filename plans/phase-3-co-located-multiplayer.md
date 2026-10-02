# Phase 3 — Co-located multiplayer

[Overarching Android roadmap](android-roadmap.md) · [Previous: Phase 2](phase-2-single-player-quality.md) · [Next: Phase 4](phase-4-release-readiness.md)

Status: accepted future outcome; product rules, technical design, and execution plan remain undecided. This outline does not authorize implementation.

## Intended outcome

A defined and tested in-person party cooking mode for people physically gathered together, with the social energy of a cooperative cooking game.

## What this phase should handle

- Owner decisions about player count, shared versus separate devices, input model, session setup, connectivity, and cooperative rules.
- A concrete multiplayer design and its effect on gameplay ownership, synchronization, pause, and session recovery.
- Implementation and verification of the selected party mode while preserving enjoyable offline single-player.

Do not choose a transport, shared-device model, or cross-platform architecture prematurely. Multiplayer leaderboard details are not yet committed.

## Dependencies and planning gate

Build on verified preceding phases. When this phase becomes next, settle the product questions, record material decisions in ADRs, and create the detailed slices, test matrix, device requirements, and acceptance criteria before implementation.

## Boundaries and related ideas

Remote matchmaking/multiplayer and iPhone are outside the current commitment. Ingredient-role division, catapults, and left/right seating are preserved inspirations rather than approved rules.

Related inspiration: [social and multiplayer concepts](../docs/ideas/inbox.md#social-and-multiplayer-concepts) and [competition concepts](../docs/ideas/inbox.md#competition-account-data-and-friends). Follow [accepted direction](../docs/direction.md) and [ADR 0002](../docs/decisions/0002-offline-first-competition-direction.md).
