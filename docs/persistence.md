# Persistence and account integration

Purpose: safely modify saves, settings, authentication, or Firebase. For game rules, see [gameplay.md](gameplay.md).

## Local stores

| Store | Owner | Contents |
| --- | --- | --- |
| `chef_prefs` | `PrefsHelper` | volume, joystick scale, language, profile fields, and aggregate stats. |
| `GameSave` | `GameActivity` / `PrefsHelper` | player position, score/failures, held item, table items, active orders, and pot states/contents/progress. Basket selection, ingredient-fetch state, and scoring streak state are not saved. |
| `ProcessManagerPrefs` | `GameManager` | legacy local high score written on game over. |

The save format is manually keyed in `GameActivity.saveGameState()` and `loadGameState()`, not versioned. Adding/reordering objects, modifying IDs, or changing a recipe/dish representation needs a migration/default strategy and a save/load smoke test. A completed loaded game should clear the save; verify this path when changing it.

## Firebase / Google sign-in

`AccountManager` uses Android Credential Manager to obtain a Google ID token, authenticates with Firebase Auth, and reads/writes Firestore. Configuration lives in `android/app/google-services.json`; treat it as sensitive configuration and do not copy it into docs.

Expected Firestore document: `chefs/{uid}` with `profile`, `stats`, and `settings` nested maps. The code also sketches social/following functionality, but it is not a complete feature.

`BaseActivity.onCreate()` calls `PrefsHelper.init(context, new AccountManager(this))`. `PrefsHelper` writes locally then calls `AccountManager.update…`; those update methods dereference `getCurrentUser()` and therefore require careful signed-out/offline handling. The original plan says Firebase may have expired and the feature needs validation; do not present cloud sync as production-ready.

## Change checklist

- Maintain a local-first outcome for settings/gameplay data unless the user explicitly changes the product decision.
- Test signed-out, no-network, first sign-in, returning sign-in, and failed Firebase requests when changing account code.
- Avoid logging identity tokens, UIDs, email addresses, or raw preference dumps.
- Document any schema change here, including migration and rollback behavior.
