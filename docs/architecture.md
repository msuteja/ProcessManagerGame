# Runtime architecture

Purpose: make structural changes to the Android app, custom game engine, map, rendering, threading, or UI wiring. For observable game rules, see [gameplay.md](gameplay.md).

## Runtime shape

```text
MainActivity ──> GameActivity / TutorialActivity
                    │
                    ├─ GameManager ──> orders, timer ticks, score, game-over callbacks
                    ├─ GameView ─────> Game draw/update loop on custom SurfaceView
                    └─ Game ─────────> map, Player, interactables, collision
                                      ├─ Pots (shared PotThreadPool)
                                      └─ Baskets (BasketManager + fetch/fill workers)
```

`GameActivity.initializeGameComponents()` composes these objects and acts as the listener for `GameManager`, ingredient fetching, basket filling, and pot progress. Android XML layouts are overlays around `GameView`; canvas objects are not Android views.

## Map and rendering

- Runtime map: `android/app/src/main/assets/map.tmj`; authoring counterpart: `Tiled stuff/map.tmj`. The project directory naming decision is recorded in [ADR 0005](decisions/0005-android-project-directory.md).
- `Game.loadMapFromJson()` reads the `Floor` tile layer and `Interactables` object layer, finds spawn tile GID `2`, and instantiates objects from their `type` property.
- It maps only the listed external tilesets to runtime sprites in code. Map object properties provide each interactable's sprites and pot settings.
- `Game.draw()` paints floor, interactables, then player onto the canvas. `GameView` runs the update/draw loop; `Game.getSleepTime()` targets 16 ms.
- Coordinates are pixels but gameplay assumes `Game.TILE_SIZE == 120`, `MAP_WIDTH == 20`, and `MAP_HEIGHT == 9`. Map resizing requires changing this coupling and testing multiple display sizes.

## State owners

| State | Owner | Notes |
| --- | --- | --- |
| Player location/motion and held item | `Player`, shared `PlayerInventory` | Moves in tile increments; collision is in `Game.canMoveTo`. |
| Map objects | `Game` | Lists are built once from the map. |
| Pot contents/state | `Pot`, `PotFunctions` | Cooking runs on a shared executor. |
| Basket contents / available ingredients | `BasketManager`, `IngredientFetchWorker` | Fetcher uses a producer/consumer queue to refill baskets. |
| Orders, score, failures, pause | `GameManager` | Main-thread handlers schedule spawning and 16-ms ticks. |
| Activity/HUD state | `GameActivity` | Receives callbacks and updates Android views. |

## Concurrency boundaries

`GameManager` handlers run on the main looper, while `PotThreadPool`, `IngredientFetchWorker`, and `IngredientBasketFiller` use executors. Several classes use locks and return defensive copies. UI changes initiated by a worker must return to the main thread; retain this boundary when refactoring. Ensure executors/handlers do not retain a destroyed activity.

## High-risk seams

- The map's external TSX paths/names, JSON object properties, Java switch cases, and asset filenames are a single integration seam.
- `GameActivity` serializes directly against object ordering (tables, pots, baskets); map reordering can silently remap saved state.
- `Pot` casts its `Context` to `GameActivity` for progress updates, so it is not reusable with another context as written.
- `Recipe` identity is name-based at submission/save boundaries; rename migrations need compatibility handling.

## Source navigation

| Concern | Primary files |
| --- | --- |
| Activity navigation/menu | `MainActivity.java`, `BaseActivity.java`, `AndroidManifest.xml` |
| Game composition/HUD/input/save | `GameActivity.java`, `activity_game.xml` |
| Canvas loop/map/collision | `GameView.java`, `Game.java`, `Player.java`, `Interactable.java` |
| Orders/time/score | `GameManager.java`, `Order.java`, `OrderAdapter.java` |
| Cooking and objects | `Pot*.java`, `Basket*.java`, `Table.java`, `SubmissionZone.java`, `RubbishBin.java` |
