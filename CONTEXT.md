# Cooking Spree domain glossary

Purpose: canonical product vocabulary. It intentionally contains no implementation decisions or plans.

| Term | Meaning |
| --- | --- |
| Casual player | The primary audience: someone seeking an approachable, low-friction game session. |
| Core game | The single-player cooking loop that remains playable without an account or internet connection. |
| Offline-first | The core game works without network access; network features enhance rather than gate play. |
| Leaderboard | An optional online comparison of authenticated player performance. The first intended form is a global, all-time high-score ranking; friends ranking follows it. |
| Co-located multiplayer | Future party play by people physically gathered together. Transport, devices, input model, and player count are undecided. |
| Android completion roadmap | The user-approved outcome sequence whose eventual completion permits beginning iPhone development. Its task-level detail and final completion definition are still to be approved. |
| Chef Code | A human-shareable identifier used to add a friend; it is distinct from an internal Firebase user ID. |
| Cloud profile | The Google-account-backed durable profile containing settings, lifetime statistics, and high score. It excludes an in-progress game. |
| Device profile | The durable profile held locally on one device. It is the source of truth until the player explicitly selects it or the cloud profile during a sync conflict. |
