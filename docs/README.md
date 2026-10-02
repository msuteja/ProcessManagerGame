# Cooking Spree documentation

Purpose: route an agent to the smallest document needed to work safely. This directory describes the checked-in app as inspected on 2026-09-21; code and Gradle files remain authoritative.

| Need | Read |
| --- | --- |
| Understand audience, product direction, online/offline boundaries, or platform scope | [product.md](product.md) |
| Understand accepted product priorities and phase boundaries | [direction.md](direction.md) |
| Capture or revisit an uncommitted inspiration | [ideas/README.md](ideas/README.md) |
| Create an asset brief or integrate a Gemini-created visual | [assets.md](assets.md) |
| Orient to the repository, run it, build it, or test it | [development.md](development.md) |
| Change gameplay, UI flow, timers, recipes, interactions, or scoring | [gameplay.md](gameplay.md) |
| Change the canvas engine, map, controls, concurrency, or screen wiring | [architecture.md](architecture.md) |
| Change sign-in, saved games, settings, local data, or Firebase | [persistence.md](persistence.md) |
| Pick up known product/technical work | [roadmap.md](roadmap.md) |
| See the long-term goal, phase summaries, and links to phase plans and ideas | [Overarching Android roadmap](../plans/android-roadmap.md) |
| Execute the detailed Phase 0 baseline recovery contract | [Phase 0 plan](../plans/phase-0-baseline-recovery.md) |
| Record or evaluate a material product/technical decision | [decisions/README.md](decisions/README.md); [repository layout](decisions/0004-project-root-repository.md); [proposed Phase 0 boundaries](decisions/0003-phase-zero-session-and-save-boundaries.md) |
| Understand the accepted Android project directory name | [ADR 0005 — Android project directory](decisions/0005-android-project-directory.md) |
| See the evidence behind the agent workflow | [research/matt-pocock-agentic-workflow.md](research/matt-pocock-agentic-workflow.md) |
| Read the repository review, corrected against current code on 2026-10-01 | [reports/2026-09-21-repository-review.md](reports/2026-09-21-repository-review.md) |
| Finish a change without leaving docs stale | [doc-maintenance.md](doc-maintenance.md) |

The top-level [AGENTS.md](../AGENTS.md) is the operating contract for AI agents, including plan/delegate/review expectations.
The top-level [CONTEXT.md](../CONTEXT.md) is the canonical product glossary.

## App in one paragraph

Cooking Spree is a landscape Android single-player cooking game. The player moves tile-by-tile around a Tiled kitchen, picks up a rotating set of ingredients, cooks exact three-ingredient recipes, and submits finished dishes before randomly spawned orders expire. Three expired orders end the game. The app has a menu, tutorial, pause/save/load flow, local preferences, and an unfinished Google/Firebase account-sync integration.

## Repository map

| Location | Purpose |
| --- | --- |
| `android/` | Android Gradle project; all runnable app code. Run Gradle here; Git is rooted at the project root. |
| `android/app/src/main/java/com/game/cookingspree/` | Java activities, game loop, domain objects, interactions, and Firebase helpers. |
| `android/app/src/main/res/` | Android layouts, UI drawables, audio, strings, and themes. |
| `android/app/src/main/assets/` | Runtime Tiled map and canvas tile images. |
| `Tiled stuff/` | Tiled authoring map and TSX tile metadata. |
| `new sprites/` | Source sprite artwork. |
| `plans/COOKING SPREE future plans.md` | Original, unprioritised backlog; see the distilled notes in `roadmap.md`. |
| `plans/android-roadmap.md` | Overarching Android plan: long-term goal, phase summaries, completion rules, and links to phase plans and ideas. |
| `plans/phase-*.md` | One plan per phase; Phase 0 contains detailed Luna slices, while later phases retain outcome outlines until ready for detailed planning. |
| `logs *.txt` | Historical debugging output; ignored by Git and not current runtime documentation. |

The project root is the sole Git repository and contains the canonical guidance, documentation, plans, map-authoring sources, and source artwork alongside the Android project.
