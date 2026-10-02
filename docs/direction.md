# Product direction

Status: accepted by the product owner on 2026-09-21. This is a living direction document, not a task list. For raw inspirations, see [ideas/inbox.md](ideas/inbox.md); for active execution planning, see [../plans/android-roadmap.md](../plans/android-roadmap.md).

## Product promise

Cooking Spree is an approachable Android cooking game for casual players. Its core single-player game works without an account or internet connection. Online features enhance play instead of gating it: players may optionally use a Google account for cloud profile storage and competitive leaderboards.

The eventual multiplayer goal is co-located party play for people physically gathered together, with the social energy of Overcooked. It is deliberately not yet a technical design: player count, device arrangement, input model, transport, and rules remain open.

## Platform boundary

Android is the only active client. iPhone implementation may begin only after the product owner defines and accepts completion of the Android roadmap. Do not add a cross-platform stack, an iPhone client, or speculative abstraction solely for that future possibility.

## Priority order

Before feature delivery, restore a buildable, testable Android baseline. The product priority after that is:

1. Competition: cloud profile, global leaderboard, then friends ranking.
2. Offline single-player quality and progression polish.
3. Co-located multiplayer.

Friends ranking is nearly as important as the global leaderboard, but follows it so account identity, upload, and ranking work is proven first.

## Online, cloud, and leaderboard boundaries

- Guest/local play is always available. If a player clears device data without linking a Google account, that local data cannot be recovered.
- Only authenticated Google-account scores are eligible for the first online leaderboard. The first leaderboard is global and all-time; seasons and remote multiplayer are deferred.
- The first leaderboard may treat submitted scores as trusted casual competition. Stronger validation/anti-cheat is a later fairness/security decision.
- A Cloud Profile contains profile details, settings, lifetime statistics, and high score. An active in-progress game remains device-local in the first version.
- On first account sign-in where local data exists, and whenever local/cloud data diverges, show a sync prompt. Present key facts for each complete profile (including high score, games played, and last-updated time) and let the player choose **Use this device** or **Use cloud**. Do not silently merge or overwrite a profile.
- Friends are added through Chef Codes, never raw backend IDs. Friends-only ranking is a near-term Competition-phase feature after the global leaderboard.

## Explicit exclusions

- Monetisation, ads, currencies, purchases, skins, and power-ups are out of scope.
- iPhone work, remote multiplayer, seasons, and advanced anti-cheat are not current commitments.
- The current visual/audio assets are not the final publication direction. A cohesive, original, appropriately licensed visual and audio pass is a release-readiness requirement.

## Visual asset workflow

Gemini is the preferred generator for new sprites, wallpapers, and similar visual assets, using the product owner's subscription through a human handoff. Agents prepare asset briefs and Gemini prompts; the product owner generates/provides the result; agents integrate and document it. See [assets.md](assets.md).

## Governance

An idea becomes committed work only after the product owner approves its outcome, priority, and acceptance criteria. Material accepted trade-offs are captured in ADRs; raw ideas remain preserved in the append-only inbox. Every completed Android milestone requires build/test evidence, manual smoke-test evidence, current documentation, and an approved PR.
