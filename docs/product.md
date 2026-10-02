# Product direction

Purpose: guide scope decisions without treating an unprioritised ideas list as a roadmap. The user owns priorities and final approval. The accepted priorities and delivery phases are in [direction.md](direction.md).

## Product promise

Cooking Spree is a casual cooking game. A player can enjoy its core single-player loop without an internet connection or account. Online features are optional enhancements: leaderboard competition and, later, social features must not gate gameplay.

The desired multiplayer direction is an in-person gathering/party game: people meet physically and play together, drawing inspiration from the social energy of Overcooked. It is not yet a specified multiplayer design or a commitment to remote matchmaking.

## Platform boundary

Android is the sole active platform. The eventual iPhone version begins only after the user has defined and accepted the Android roadmap as complete. Until then, agents should favour clean Android seams where they are low-cost, but must not introduce cross-platform architecture or a second client without an approved decision.

## Scope decisions awaiting the user

- The Android roadmap, its ordering, and its definition of completion.
- What local multiplayer means in practice: shared screen/device, local network, multiple devices, maximum players, and input model.
- Leaderboard rules: account requirement, offline score handling, anti-cheat expectations, ranking period, and privacy.
- Accessibility, age/content rating, monetisation, and visual/brand direction.

Record resolved commitments in [decisions/](decisions/README.md), then update the roadmap and affected implementation docs.
