# Phase 1 — Competition

[Overarching Android roadmap](android-roadmap.md) · [Previous: Phase 0](phase-0-baseline-recovery.md) · [Next: Phase 2](phase-2-single-player-quality.md)

Status: accepted phase outcome; detailed execution plan pending Phase 0 implementation and verification. This outline does not authorize implementation.

## Intended outcome

Optional online competition enhances the game while guest/offline single-player remains fully usable. Deliver cloud profiles first, a global all-time leaderboard next, then Chef-Code friends ranking.

## What this phase should handle

- Google account identity and a Cloud Profile containing profile details, settings, lifetime statistics, and high score. Active games remain device-local.
- Explicit local/cloud conflict handling: show the relevant facts for each complete profile and let the player choose **Use this device** or **Use cloud**. No silent merge or overwrite.
- Authenticated Google-account score submission and global all-time ranking, followed by adding friends through Chef Codes and friends-only ranking.
- Cloud/backend correctness and failure behavior, including numeric settings hydration, nested profile query/UID paths, Chef Code uniqueness, and cloud reads echoing writes.
- Firestore rules and backend schema validation before treating the online feature as ready.

## Dependencies and planning gate

Use Phase 0 verification evidence and the actual resulting sync/session boundaries when drafting tasks. Backend schema/rules access and owner-approved competition acceptance criteria are needed. Carry forward the unresolved account findings from the [repository review](../docs/reports/2026-09-21-repository-review.md); validate them against the code at planning time.

The detailed plan will add bounded slices, affected files, tests for guest/offline/network/account failures, conflict-choice scenarios, ranking acceptance criteria, documentation owners, and proposed backend/compatibility decisions. Draft it at Phase 0 closeout before implementation begins.

## Boundaries and related ideas

Remote multiplayer, seasons, advanced anti-cheat, monetisation, and iPhone remain outside the accepted scope. The first leaderboard may use trusted casual scores as specified in [accepted direction](../docs/direction.md); do not imply strong score validation exists.

Related inspiration: [account/local play/cloud save](../docs/ideas/inbox.md#account-local-play-and-cloud-save), [competition/accounts/friends](../docs/ideas/inbox.md#competition-account-data-and-friends), and [preference/account validation](../docs/ideas/inbox.md#preference-migration-and-account-validation). Accepted direction is recorded in [ADR 0002](../docs/decisions/0002-offline-first-competition-direction.md); other ideas need owner approval before becoming tasks.
