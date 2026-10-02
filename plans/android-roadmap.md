# Cooking Spree — overarching Android roadmap

Status: living. Product direction accepted 2026-09-21. Planning documents reorganized 2026-10-01; phase scope and approval status are unchanged. Phase 0 has a detailed draft awaiting owner approval/execution. Later phases have outcome outlines and planning prerequisites, not approved implementation tasks.

## Long-term goal

Cooking Spree should become an approachable, polished Android cooking game for casual players. Its single-player core remains enjoyable without an account or internet. Optional Google cloud profiles and leaderboards add competition, followed by an in-person party cooking mode for people gathered together. The Android release should have cohesive original/licensed artwork and audio and meet an owner-approved definition of completion.

Android is the only active platform. iPhone work starts only after the owner establishes and accepts Android-roadmap completion. Monetisation, currencies, skins, power-ups, remote multiplayer, seasons, and advanced anti-cheat are not current commitments. The authoritative product priorities and boundaries are in [accepted direction](../docs/direction.md) and [ADR 0002](../docs/decisions/0002-offline-first-competition-direction.md).

## Phases and their plans

Execute in order. Each phase has its own Markdown file; this document owns the overall sequence, stage summaries, and links.

| Phase and plan | What should be handled in this stage | Status |
| --- | --- | --- |
| [0 — Baseline recovery](phase-0-baseline-recovery.md) | Restore the build and meaningful tests; make signed-out/offline play safe; fix session/worker/render/input cleanup, pause/tutorial lifecycle, destructive saves, and unsafe legacy loading; gather device evidence. | Detailed Luna execution draft; awaiting owner approval/execution. |
| [1 — Competition](phase-1-competition.md) | Optional Google cloud profile; player-selected local/cloud conflict handling; global all-time leaderboard, then Chef-Code friends ranking; account/schema/sync and backend-rule correctness. | Accepted outcome; detail after Phase 0 implementation and verification. |
| [2 — Single-player quality](phase-2-single-player-quality.md) | Select and deliver tutorial, save continuity/compatibility, map/device, rendering, and difficulty improvements for a polished offline game. | Accepted outcome; individual improvements require selection; detail when next. |
| [3 — Co-located multiplayer](phase-3-co-located-multiplayer.md) | Define player/device/input/connectivity/rules, then implement and verify an in-person party mode while preserving single-player. | Accepted future outcome; product and technical design undecided. |
| [4 — Release readiness](phase-4-release-readiness.md) | Cohesive licensed/original visuals/audio, credits, privacy/backup decisions, selected publishing requirements, release evidence, and an approved Android completion checklist. | Accepted outcome; release criteria await owner decisions. |

## Where all the ideas live

- [Idea inbox](../docs/ideas/inbox.md): the preserved, append-only collection of app inspirations, grouped by accounts, competition, quality, multiplayer, customisation, and release. It includes ideas that are deferred or outside the current roadmap.
- [Idea inbox guide](../docs/ideas/README.md): how ideas are captured and promoted into committed work.
- [Original future-plans document](COOKING%20SPREE%20future%20plans.md): the original detailed brainstorming source, preserved intact.

An idea appearing in these documents is not automatically approved work. Only the owner promotes it into a phase by approving its outcome, priority, and acceptance criteria. Keep original wording; mark ideas linked, deferred, or superseded instead of deleting them.

## Planning and execution protocol

Only the next phase should become task-level detailed. Phase 0 is detailed now; retain later phases as outlines until implementation evidence and owner decisions make their scope concrete. Detail Phase 1 at Phase 0 closeout, then Phase 2 when it becomes next. The phase files carry their review-derived issues and dependencies so that context is preserved without speculative task lists.

Before a phase begins, its file must contain an approved outcome, scope/exclusions, bounded slices, dependencies, acceptance criteria, verification, documentation changes, and links to relevant ideas/ADRs. Material architecture, persistence, online, platform, or product decisions use [decision records](../docs/decisions/README.md). Revisions preserve earlier detail by marking it deferred or superseded rather than silently removing commitments.

Sol/Terra plans and orchestrates; Luna implements bounded slices; the orchestrator reviews, integrates, verifies, and reports evidence. Follow [agent governance](../docs/decisions/0001-agent-governance-and-delivery.md). Phase 0's detailed file includes the Luna handoff and slice gates.

## Verification and completion

Implementation means the planned changes have been made. Verification means the build, focused tests, review, and relevant device/emulator smoke checks demonstrate the phase's acceptance criteria. Missing device evidence leaves verification pending.

A phase is complete only when its criteria are met, automated and manual evidence is recorded, documentation is current, and the owner approves its PR. Opening a PR does not authorize merging. Review the next phase's plan against the actual result before starting it.

## Supporting records and repository boundary

- [Repository review](../docs/reports/2026-09-21-repository-review.md): validated defects and limitations that inform phase planning.
- [Development and verification guide](../docs/development.md): commands, device requirements, and smoke checks.
- [Documentation index](../docs/README.md): canonical project documentation navigation.
- [Repository layout decision](../docs/decisions/0004-project-root-repository.md): project-root Git and canonical documentation placement.

The project root is the Git repository; `android/` contains the Android Gradle project, as recorded in [ADR 0005](../docs/decisions/0005-android-project-directory.md). The canonical documentation and phase contracts are committed together with app code, so PR reviewers can read the complete project context in one repository.
