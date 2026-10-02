# Phase 0 — Baseline recovery

[Overarching Android roadmap](android-roadmap.md) · Previous phase: none · [Next: Phase 1](phase-1-competition.md)

## Contract and evidence

**Outcome:** a reproducibly buildable Android app whose existing single-player loop can be played, paused, saved, loaded, and ended while signed out and offline, without the validated crashes, destructive saves, duplicate tutorial sessions, or abandoned background work.

**Status:** draft for approval. The owner requested a detailed plan to be executed by Luna; this document does not claim that the owner has accepted its proposed pause/save policies or that implementation has begun.

Inputs:

- [Corrected repository review](../docs/reports/2026-09-21-repository-review.md), checked against source on 2026-10-01.
- [Accepted direction](../docs/direction.md), [ADR 0001: delivery](../docs/decisions/0001-agent-governance-and-delivery.md), and [ADR 0002: offline-first direction](../docs/decisions/0002-offline-first-competition-direction.md).
- [Existing Phase 0 outline](../docs/roadmap.md): its build, test, offline, smoke-test, and owner-review criteria are incorporated here; this file supplies the missing bounded slices.
- [Proposed ADR 0003](../docs/decisions/0003-phase-zero-session-and-save-boundaries.md): session ownership, pause behavior, and legacy-save compatibility. Owner acceptance is required before implementing those decisions.
- Inbox context: [account/local play](../docs/ideas/inbox.md#account-local-play-and-cloud-save), [preference validation](../docs/ideas/inbox.md#preference-migration-and-account-validation), and [game quality/tutorial](../docs/ideas/inbox.md#game-quality-and-tutorial). Only the defect recovery described here is proposed; broader inspirations remain uncommitted.

## Scope and explicit deferrals

Include all report P1 findings, render/input/session cleanup, manual/lifecycle/tutorial pause consistency, safe loading of the existing save format, focused regression tests, and removal of raw profile logging. Repair small adjacent defects only when necessary to meet a listed acceptance criterion; report unrelated discoveries without silently expanding scope.

Defer to Phase 1: backend/rules validation, first-account sign-in redesign, local/cloud conflict UI, Chef Code schema/uniqueness/following fixes, numeric cloud hydration, leaderboards, and account data migration. Only signed-out safety, failure containment, removal of static Activity retention, and logging cleanup touch account code here. Do not present existing account sync as production-ready or change cloud conflict policy implicitly.

Defer to Phase 2: action-gated tutorial redesign, basket/fetch/streak save continuity, stable map/recipe IDs and a new versioned save schema, responsive map/layout changes, balance, catalogue-wide refactoring, and rendering optimization. Phase 0 validates the current map/catalogue and legacy keys; it does not promise compatibility with future renamed recipes or reordered maps. Basket selection is regenerated on load and streak resets remain documented limitations.

No iPhone, multiplayer, monetisation, artwork/audio replacement, SDK/platform expansion, backend deployment, or production publishing. Preserve Firebase configuration without reproducing it in logs, docs, commits, or handoff output.

## Execution and ownership

Sol/Terra orchestrates; **Luna implements**. Execute slices 0A–0F sequentially because they overlap in `GameActivity`, session timing, and tests. Do not send the entire phase as one unchecked rewrite or run overlapping mutations in parallel.

For each slice, the orchestrator gives Luna the slice ID, required readings, bounded files, acceptance criteria, predecessor result, and known limitations. Luna returns changed files, rationale, checks and results, documentation updates, and unresolved risks. The orchestrator inspects the changes, resolves integration concerns, runs the relevant checks, and advances only when that slice's gate is met. A blocked device check may leave implementation reviewable but cannot be recorded as a pass or phase completion.

Use the `gpt-6-luna` agent for implementation when execution is requested. No agent or separate chat is launched by this planning step. Read the project-root `AGENTS.md` before execution.

The project root is the Git repository and `android/` is the Android Gradle project, as recorded in [ADR 0005](../docs/decisions/0005-android-project-directory.md). Run Git from the root and Gradle from `android/`. Before mutation, inspect the branch, status, and applicable instructions; preserve unrelated changes. Do not claim the tree is clean without checking it. Canonical plans and docs are in this repository and are included in the same PR as implementation changes.

## Test environment setup — begin with 0A

Environment setup is part of execution, not an assumed owner prerequisite. The agent performs the following preflight before relying on test results and prepares device access early enough for lifecycle tests in 0C onward:

1. Locate the actual Android SDK and supported JDK; verify Java/Gradle compatibility, SDK platform 35 for compilation, platform-tools/ADB, and emulator tooling. Use installed paths explicitly when tools are absent from PATH. Inspect existing SDK, AVD, and device configuration before installing or changing anything.
2. Check attached devices with `adb devices -l` and list available virtual devices using the installed emulator-management tool. Inspect the owner's AVD location explicitly if the execution environment uses a different Windows user/profile; an empty list under a sandbox profile does not prove no owner AVD exists.
3. Reuse a suitable development AVD/device. If none exists, create a dedicated Cooking Spree test AVD using an API 35 system image compatible with the host; API 34+ is the app's runtime requirement. Install only missing development packages needed for this setup. No Google-account login is required for Phase 0 guest/offline tests. Do not wipe an existing personal device or unrelated AVD.
4. Start the AVD through command-line tooling, or use an owner-started device. Android Studio does not need to remain open. Wait for ADB to report the selected device ready and confirm Android has completed booting. Record its serial, API level, image, and relevant display configuration; select one explicit target if several devices are connected.
5. After build recovery, run `gradlew.bat assembleDebug` and `gradlew.bat testDebugUnitTest` from `android/`. With the selected device ready, run `gradlew.bat connectedDebugAndroidTest`; build/run the app for the acceptance scenarios. Capture test reports, relevant redacted logs, labelled screenshots of materially changed scenes and key acceptance states, and short recordings or screenshot sequences where interaction/lifecycle behavior cannot be demonstrated by a still image. Capture additional screenshots for device/visual failures. Establish controlled guest/offline state on the dedicated test device without resetting the owner's data.
6. Record the reproducible launch/test commands and any required setup in `docs/development.md` and repository documentation. Local unit tests do not need a running emulator. Automated device scenarios must assert outcomes, not merely launch the app; use a small test harness for canvas game state alongside tests through real Android controls.

The previous inspection found installed SDK/ADB/emulator executables, no attached device, and no AVD listed in the inspected environment; recheck at execution. If SDK downloads, license acceptance, hardware virtualization, Windows features, or device authorization actually require owner action, report the specific missing prerequisite and steps. Continue independent build/unit-test work while device setup is blocked. Do not mark device verification passed until it ran.

Setup references: [Android virtual device management](https://developer.android.com/studio/run/managing-avds), [command-line emulator startup](https://developer.android.com/studio/run/emulator-commandline), and [Android testing fundamentals](https://developer.android.com/training/testing/fundamentals).

## 0A — verify and stabilise the build

**Dependencies:** none. **Files:** `android/app/build.gradle.kts`, project Gradle configuration/wrapper, version catalogue, and only source/resource files demonstrated to cause compilation failure. Documentation: `docs/development.md`.

Tasks:

1. Run `assembleDebug` and `testDebugUnitTest` from tracked inputs. The unit-test task succeeded on 2026-10-03; verify that result is repeatable. If the historical 94-error resource-symbol failure returns, inspect namespace, resource inputs, generated symbols/classes, and compiler classpath before selecting a fix; do not assume every referenced resource is absent.
2. Fix only a demonstrated configuration/input failure. Do not hand-write `R.java`, remove Firebase configuration, rename the application ID, blanket-disable checks, or upgrade dependencies/platform levels without demonstrated necessity.
3. Correct the stale instrumented application-package assertion. Keep the current Android/API boundary.
4. Run `gradlew.bat assembleDebug` and `gradlew.bat testDebugUnitTest` from `android/`. Confirm a subsequent build succeeds without manually editing generated outputs. A targeted clean reproduction is appropriate if stale artifacts are part of the cause.

**Gate:** both commands succeed repeatedly from tracked/configured inputs; any reproduced failure's root cause and fix are documented. Starter unit-test success is only a build gate, not gameplay evidence. If credentials or unavailable dependencies block recovery, identify the exact dependency and continue unaffected investigation.

## 0B — signed-out and unavailable-cloud safety

**Dependencies:** 0A. **Files:** `BaseActivity.java`, `MainActivity.java`, `AccountManager.java`, `util/PrefsHelper.java`, `GameActivity.java` stat completion paths, and focused tests under `android/app/src/test/` or `androidTest/`. Java paths are under `android/app/src/main/java/com/game/cookingspree/`. Documentation: `docs/persistence.md`.

Tasks:

1. Make local preference/stat writes succeed independently of authentication and Firestore tasks. Obtain the current user once per update; skip cloud writes when absent and attach bounded, non-sensitive failure reporting when present. Avoid synchronously waiting for network operations.
2. Make the initial joystick selection reflect the saved setting without treating programmatic hydration as a user edit. Verify both menu and game launch.
3. Remove the static reference chain from `PrefsHelper` to an Activity. Use application-context storage and an Activity-free sync boundary, keeping credential prompts/dialogs activity-owned. Avoid replacing the leak with a static callback that captures the Activity.
4. Contain Firebase initialization/task failures so guest gameplay/settings remain available. Inspect initialization separately from network failure; do not swallow arbitrary gameplay exceptions as an offline workaround.
5. Remove preference dumps and all identified raw chef-name logging. Preserve useful redacted diagnostics.

**Gate:** with no signed-in user and no network, fresh launch, joystick/volume changes, a complete game-over sequence, high score, average score, and games-played updates succeed locally. Restarting the app preserves local data. A simulated sync failure cannot abort those writes. No static owner retains a destroyed Activity, and raw profile values are absent from these log paths.

**Tests:** null-user update, failed-sync/local-write independence, initial selection without upload, and game-over stats/save clearing. Use a small fake sync seam for deterministic tests; do not require a live Firebase account/backend for Phase 0 gates.

## 0C — single session, cancellation, and render/input teardown

**Dependencies:** 0B. **Files:** `GameActivity.java`, `TutorialActivity.java`, `GameManager.java`, `GameView.java`, `Game.java`, `Player.java`, `Pot.java`, `PotFunctions.java`, `PotThreadPool.java`, `IngredientFetchWorker.java`, `IngredientBasketFiller.java`, `IngredientQueue.java`; touch only responsibilities needed for session ownership. Documentation: `docs/architecture.md`.

Tasks:

1. Select the normal/tutorial layout before composing components. Initialize exactly one manager, render binding, pot pool, and fetch/fill chain per active activity session; prevent tutorial superclass setup from creating a hidden first game.
2. Expose idempotent close/cancel operations. Reject work after closure, stop handlers, interrupt sleeping/queue-waiting work, and exit interrupted tasks before food creation, basket mutation, or callbacks. Dispose the filler through its owning fetcher.
3. Replace the pot-to-`GameActivity` cast with a narrow listener. Suppress queued UI callbacks after session closure as well as worker callbacks; a main-thread runnable already posted needs the same ownership check.
4. Give the render loop safely published state, null/init protection, prompt interrupt exit, and bounded teardown. Prevent a new surface from launching a second loop while the old one remains active; do not hold game-state locks while joining workers.
5. Cancel all held-direction callbacks on pause/stop/destruction and handle simultaneous direction touches without orphaning a self-reposting runnable. Reset held movement at the same boundary.

**Gate:** repeated game/tutorial entry/exit leaves one active session and no old spawning/ticking/fetching/cooking work. Closing during cooking, fetching, or a blocked queue produces no late session mutation or UI callback. Surface recreation leaves one render loop; teardown has a defined bound and does not hang the UI.

**Tests:** idempotent closure, interrupted cook without food/callback, interrupted consumer without continuation, closed-owner callback suppression, two-direction press/release, and tutorial initialization count. Device lifecycle checks verify destruction/recreation; garbage collection timing alone is not proof of cleanup.

## 0D — consistent pause and tutorial lifecycle

**Dependencies:** 0C and acceptance of ADR 0003. **Files:** session/lifecycle paths in `GameActivity.java`, `TutorialActivity.java`, `GameManager.java`, `Game.java`, `Player.java`, worker timing in `PotFunctions.java` and `IngredientFetchWorker.java`, and `ElapsedTimer.java` if needed. Documentation: `docs/gameplay.md`, `docs/architecture.md`.

Tasks:

1. Track manual-menu pause, background pause, tutorial pause, and terminal/closed state independently. Gameplay runs only when no pause reason applies. Returning to the foreground removes only the background reason; it cannot resume a manual menu or tutorial pause.
2. Freeze order countdown/spawning, player progression/input, cooking, and ingredient fetch/fill transitions at pause. Preserve remaining in-memory work rather than completing it immediately or restarting it on resume. Rendering may continue to show the paused scene. Make boundary transitions safe when a worker tick and pause arrive together.
3. Keep UI/menu visibility consistent with pause state. Prevent ordinary gameplay interaction while paused or over. Preserve the existing tutorial's intended movement demonstration through an explicit allowed step; do not redesign the tutorial.
4. Initialize tutorial steps before checking their count, hide the ordinary pause button during the walkthrough, and prevent inherited `onResume` from undoing tutorial pause. Restore normal controls after skip/completion.
5. Make game over terminal and idempotent: one dialog, one local stat update, one save clear; remaining callbacks and multiple expiries in one update cannot finalize the game repeatedly.

**Gate:** pause for at least 10 seconds while cooking and swapping; order/cook/fetch/player state is unchanged after pause settles. Resume continues once with remaining time. Backgrounding while manually paused returns to a visible paused menu. Backgrounding a running game resumes safely. Tutorial starts paused and creates no hidden orders; skip/completion produces one playable session.

**Tests:** combined pause reasons, remaining duration across pause, resume idempotence, tutorial first resume, multiple expiries/finalization once. Control time with a fake clock where practical; avoid sleep-heavy unit tests.

## 0E — non-destructive saves and validated legacy loading

**Dependencies:** 0D and acceptance of ADR 0003. **Files:** save/load paths in `GameActivity.java`, `Pot.java`, `PotFunctions.java`, `Player.java`, `GameManager.java`, `Order.java`, and `PrefsHelper.java`; small snapshot/parser/validator classes may be added under the existing package. No runtime/authoring map edits or catalogue renames. Documentation: `docs/persistence.md`, `docs/gameplay.md` where behavior changes.

Tasks:

1. Snapshot completed food without consuming it. Initialize cooking recipe/progress before publishing COOKING; loaded cooking must restore both before workers resume. Snapshot each pot consistently rather than separately observing state, ingredients, recipe, and food.
2. Save only through the paused boundary, serialize a validated complete snapshot, and write existing keys in one editor operation. A failed capture/write keeps the previous save and current game intact; display a single success or failure result, not the current unconditional extra success toast.
3. Capture a stable logical movement tile rather than a transient interpolated coordinate. Keep live paused state unchanged. Validate legacy coordinates; normalize a fractional legacy location only to a valid nearby traversable tile, otherwise reject safely. Explain this compatibility behavior in persistence documentation.
4. Parse all legacy values into a candidate state before mutating live gameplay. Bound counts against catalogue/current map/capacities; validate field types, positions/collision, nonnegative score, failures below game-over threshold, ingredient IDs, known recipe names (including Waste where legitimate), pot state/content/progress consistency, and order times. A finished/dead save must not invoke game-over during partial restoration.
5. Apply a validated candidate before starting its orders/cooking workers, with the fresh game's timers/refill tasks unable to race restoration. Reject an invalid candidate without a partially loaded session; preserve stored data, show a recovery message, and offer a new game. Do not silently turn unknown recipes into Waste or drop saved orders.
6. Retain current key names, map list ordering, and valid legacy saves. Test legacy Waste pots and partially filled EMPTY pots explicitly. Schema versioning, stable object/recipe IDs, basket/fetch/streak continuity, and automatic destructive migration are deferred.

**Gate:** saving twice with DONE food leaves it collectible exactly once. Load a COOKING save, save again before completion, then reload and collect once. EMPTY pots with one/two ingredients, held items, table items, orders, score, and failures survive a valid round trip. Failed saves retain the last valid save. Malformed, unknown-recipe, out-of-range, and completed-game saves never partially restore or award stats. Ending a loaded game clears its save and subsequent Load reports no save.

**Tests:** DTO/legacy-parser validation with wrong types/counts/IDs/states/times/coordinates, non-consuming snapshots, resumed-recipe snapshot, repeated save/load, recovery without mutation, and game-over save clearing. Test map-count mismatch rejection; reordered maps with identical counts remain a documented limitation until stable IDs are introduced.

## 0F — integration evidence and delivery

**Dependencies:** 0A–0E. **Files:** meaningful tests under `android/app/src/test/` and `android/app/src/androidTest/`; documentation and PR template only unless verification exposes an in-scope defect.

1. Replace the arithmetic starter with regression coverage for exact recipe matching (duplicate potatoes included), wrong/extra ingredients, order expiry, completion points/streak reset, and three-failure finalization. Add deterministic clock/sync seams only where needed; retain the current documented balance. Correct resource/package smoke checks where useful.
2. Run `gradlew.bat assembleDebug` and `gradlew.bat testDebugUnitTest`. With an attached API 34+ emulator/device, run `gradlew.bat connectedDebugAndroidTest`. Record command, result, date, device/API for device checks, and the behavior each test protects. A skipped check is not a pass.
3. Complete the smoke matrix below offline/signed out. Repeat lifecycle entry/exit and surface recreation checks at least five times. Test game-over save/stat behavior both from a new session and from a loaded one.
4. Update the owning documentation with actual final behavior, commands, limitations, and accepted decisions. Mark individual report findings resolved only with a source/test reference and date; keep deferred findings visible. Do not rewrite the report as if the original findings never existed.
5. Review all Luna changes against this contract, confirm no accidental configuration/asset exposure, and prepare a PR using `.github/pull_request_template.md`. Include changelog, reason, material trade-offs, relevant decisions, deferred risks, command/test results, and device evidence. Attach labelled screenshots for every materially changed scene, using before/after views when useful; attach a short recording or concise screenshot sequence for interaction or lifecycle behavior that a still image cannot prove. Record the device/emulator and API level, note any unverified scenario, and redact sensitive data. Commit messages follow Conventional Commits. The owner approves the PR; do not merge automatically.

**Gate:** evidence supports every Phase 0 criterion and the owner approves the PR. Without device evidence, status is implementation complete/device verification pending, not Phase 0 complete.

## Manual acceptance matrix

| Scenario | Expected result |
| --- | --- |
| Fresh launch signed out, airplane mode | Menu and game open; saved joystick/volume work; no crash or network gate. |
| Valid dish, duplicate-ingredient recipe, invalid dish | Correct dish/Waste results; matching submission consumes once and awards the current rule's points. |
| Basket, table, rubbish, collision | Existing interactions and tile movement work; no stuck swap blocker after a normal fetch. |
| Three order failures | Exactly one game-over/stat update; high score remains local; no further order spawning. |
| Pause during cook/fetch; wait 10 seconds | Gameplay work freezes after transition, resumes once, and preserves remaining progress. |
| Pause menu → background → foreground | Pause/menu state persists; Resume is explicit. |
| Running game → background → foreground | Background time does not expire orders or complete cooking/fetching. |
| Two simultaneous direction touches; release; exit | Movement stops and no old input runnable continues. |
| Save DONE pot twice; resume/collect | Saving does not remove food; collect yields exactly one item. |
| Load COOKING pot → save again → reload | No null recipe/progress; cooking resumes and completes once. |
| Load malformed or completed legacy save | Visible recovery, no partial state/stat award; existing stored data is not silently deleted. |
| Complete a loaded game → Load again | Saved run is cleared; no replay from the completed save. |
| Enter/skip/complete tutorial; repeat five times | One session, correct tutorial pause/controls, no hidden game-over callbacks. |
| Destroy/recreate while workers run or wait | Workers/callbacks close safely, one new render loop, no UI teardown hang. |

## Dependencies and owner involvement

- The orchestrator can investigate/fix the build, delegate bounded Luna work, write tests, review changes, and prepare a PR after this plan is approved and execution is requested.
- Owner review covers the detailed scope and proposed ADR 0003 before dependent work. No live Firebase access or backend changes are required for this phase's guest/offline gates.
- A compatible device/emulator is required for completion evidence. The agent owns the environment preflight/setup above and can launch a configured AVD without Android Studio. Ask the owner only for an actual missing prerequisite the agent cannot resolve or device-only observations needing their participation.
- If the chosen fix would reset valid saves, replace the save schema, alter cloud conflict behavior, change gameplay balance, or expand platforms, stop that dependent work and present a revised concrete proposal. Do not infer approval from this plan.

## Ready-to-use Luna slice handoff

> Implement Phase 0 slice **[0A–0F]** from the approved `plans/phase-0-baseline-recovery.md` contract. Read root `AGENTS.md` and the slice's owning docs. Predecessor evidence: **[results]**. Approved decisions: **[ADR status]**. Stay within the listed files/responsibilities; preserve unrelated changes and Firebase configuration. Implement the slice's acceptance behavior with focused regression tests and its documentation updates. Run its available checks from `android/`. Return changed files, rationale, actual test/manual results, and remaining risks; identify missing device/backend evidence honestly. Do not implement later phases, merge a PR, or change unapproved product/persistence decisions. The orchestrator will inspect and verify this slice before assigning the next.
