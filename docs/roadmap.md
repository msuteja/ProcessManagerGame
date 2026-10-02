# Known direction and limitations

Purpose: preserve historical planning context, not an automatic implementation queue. Source: `plans/COOKING SPREE future plans.md`. The accepted product priorities are in [direction.md](direction.md), and the living execution roadmap is [../plans/android-roadmap.md](../plans/android-roadmap.md). iPhone work begins only after the product owner accepts Android-roadmap completion.

## Important unfinished work

- Make gameplay viable when signed out and offline; keep local device data authoritative, and make later cloud sync explicit. Optional leaderboards must not become a requirement to play.
- Validate/fix settings, profile, and stats migration between preferences and Firebase. Store joystick choice robustly.
- Build a leaderboard, friends/following, and an improved action-gated tutorial.
- Make the map/UI robust across device dimensions.
- Rebalance difficulty, then consider skins, power-ups, currency, multiplayer, and store ideas. The multiplayer vision is a co-located party mode, not a commitment to remote online multiplayer.
- Audit asset/music licensing and visual consistency before distribution; publishing is not complete.

## Current technical caveats

- The current Gradle test task fails at Java compilation because generated local `R` resource classes are incomplete; details and the reproduction command are in [development.md](development.md).
- Firebase was noted as expired/unverified in the original plan.
- The game contains only starter automated tests.
- Layout/map dimensions are deliberately fixed in several code paths.
- `SimpleTutorialActivity` is unused; decide whether to remove or revive it before investing in a second tutorial path.
- Old files and comments use “process” for what the UI now calls an order. Preserve behavioral understanding while naming new work consistently.

For implementation sequencing and agent delegation, use [AGENTS.md](../AGENTS.md). For exact game behavior, use [gameplay.md](gameplay.md).
