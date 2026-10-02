# Gameplay reference

Purpose: change rules, interactions, HUD, recipes, scoring, tutorial wording, or balance without first tracing the whole application. For object construction and rendering, see [architecture.md](architecture.md).

## Player loop

1. `GameManager` spawns an order every 5–13 seconds, up to five active orders. Each random order lasts 60–120 seconds.
2. The player selects/swaps the ingredient set shown in baskets, moves to a basket, and interacts to hold one item.
3. Interacting with an empty pot deposits an ingredient. At three ingredients it cooks for the map-configured time (currently 6 seconds).
4. A matching recipe produces that named dish; a non-matching set produces `Waste`. Collect a finished dish with empty hands.
5. At a submission zone, the first active order with the same recipe name completes. A completion awards `100 × current streak`; the streak rises when completions are within 10 seconds.
6. An expired order increments failures and is removed. Three failures end the game and persist a high score.

## Recipes and ingredients

`Recipe.getDefaultRecipes()` is the single current recipe catalogue. Ingredient IDs are fixed in `Recipe`: carrot `0`, potato `1`, onion `2`, cabbage `3`, tomato `4`.

| Dish | Exact ingredients |
| --- | --- |
| Tomato Soup | tomato, carrot, onion |
| Veggie Stew | cabbage, potato, carrot |
| Mashed Potato | potato, potato, onion |
| Salad | tomato, potato, carrot |

Recipe matching counts duplicate ingredients. When adding recipes or ingredients, update this catalogue, UI images/table sprites, Tiled properties, and save/load compatibility together.

## Interactions

| Object | Behaviour | Source |
| --- | --- | --- |
| Basket | Gives its configured ingredient only when hands are empty. | `Basket.java` |
| Pot | Holds three ingredients; state is `EMPTY → COOKING → DONE`; only empty-handed players collect. | `Pot.java`, `PotFunctions.java` |
| Table | Stores one held item or returns its item to empty hands. | `Table.java` |
| Submission zone | Consumes a cooked dish only when an active order has the same recipe name. | `SubmissionZone.java` |
| Rubbish bin | Discards held items; ten empty interactions display an Easter-egg toast. | `RubbishBin.java` |

## Screens and controls

`MainActivity` opens a normal game, load flow, or `TutorialActivity`; it also owns settings and credits. `GameActivity` overlays the custom `GameView` canvas with orders, score/failure count, inventory, ingredient swapping, interaction, pause/save/settings, and directional touch controls. `TutorialActivity` subclasses `GameActivity` and advances a text-overlay walkthrough; `SimpleTutorialActivity` exists but is not declared in the manifest.

Movement advances by one 120-pixel tile, interpolated in `Player`; an occupied interactable tile blocks movement. Interaction requires orthogonal adjacency. The game is landscape-only and is currently coded around a 20×9 map.

## Balance and rule change checklist

- Order cadence/cap/failure limit/scoring: `GameManager.java`.
- Order duration: `Order.java`.
- Recipe catalogue/matching: `Recipe.java`.
- Pot capacity/timing: `PotFunctions.java` and Tiled pot `cooking_time` properties.
- Tutorial claims: `TutorialActivity.java`.
- Saved-game schema: `GameActivity.java` and [persistence.md](persistence.md).

Test one complete order, one expiry/game-over path, all four interaction outcomes, pause/resume, and save/load after any rule change.
