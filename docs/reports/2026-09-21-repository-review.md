# Cooking Spree repository review — 2026-09-21

## Scope and limitations

This artifact records the repository review and the historically reported Codex Security Standard scan. The code-review findings were checked and corrected against the current source on 2026-10-01. This update is a source review, not a new security scan; the historical external scan bundle was not available for independent verification.

The requested `code-review` workflow is a two-axis review of a Git diff from a named fixed point. Cooking Spree is a directory snapshot with no Git repository or history, and no fixed point, issue, or implementation spec was supplied. Consequently, a Standards or Spec diff review could not be performed honestly. It is not a pass or a zero-finding result.

A future diff-based review would require a Git baseline and originating issue/spec. Neither is required to address the snapshot findings below.

## Repository code review

This is a snapshot code review of the checked-in Android source, rather than the diff-based review described above. Priorities use P1 (fix before normal use), P2 (fix before expanding the affected feature), and P3 (quality/workflow improvement).

### P1 — signed-out startup, preference changes, and game-over stats can crash

`BaseActivity.onCreate()` always constructs and registers an `AccountManager`. Settings, profile, and stat setters write locally and then attempt a cloud update. However, `AccountManager.updateSetting`, `updateStat`, and `updateProfileField` immediately dereference `getCurrentUser().getUid()` without checking for a signed-in user.

This can happen before the user changes a setting: `BaseActivity.java:49–60` posts an initial joystick radio-button selection after installing a listener that calls `PrefsHelper.setJoystickScale`. The menu/game layouts have no active initial checked selection, so initialization reaches the unsafe update outside the activity's `onCreate` catch. Game-over stat setters (`GameActivity.java:1152–1179`) can also throw, aborting subsequent aggregate-stat updates and save clearing.

Evidence: [`PrefsHelper.java`](../../android/app/src/main/java/com/game/cookingspree/util/PrefsHelper.java) lines 24–139 and [`AccountManager.java`](../../android/app/src/main/java/com/game/cookingspree/AccountManager.java) lines 320–333.

Make the local preference write unconditional, but make synchronization best-effort: obtain the user once, return when it is null, and attach failure handling to the Firestore task. This preserves the documented offline/single-player behavior and prevents settings changes from depending on sign-in.

### P1 — worker executors are never stopped and retain activity state

`PotThreadPool`, `IngredientFetchWorker`, and `IngredientBasketFiller` each own executors but expose no shutdown/cancellation method. `GameActivity.onDestroy()` stops only `GameManager` and releases audio. Pot tasks capture and cast the activity context for progress callbacks, so a queued or running task can retain a destroyed activity and later try to update it.

Evidence: [`PotThreadPool.java`](../../android/app/src/main/java/com/game/cookingspree/PotThreadPool.java), [`IngredientFetchWorker.java`](../../android/app/src/main/java/com/game/cookingspree/IngredientFetchWorker.java), [`IngredientBasketFiller.java`](../../android/app/src/main/java/com/game/cookingspree/IngredientBasketFiller.java), and [`GameActivity.java`](../../android/app/src/main/java/com/game/cookingspree/GameActivity.java) lines 1031–1044.

Give each owner a `close()`/`shutdown()` operation, call it from the activity lifecycle, interrupt blocking queue waits, and replace the `GameActivity` cast with a lifecycle-aware progress callback. Add an instrumented lifecycle test that starts cooking/fetching, destroys the activity, then verifies no callbacks reach it.

Interruption alone is insufficient: both cooking methods catch `InterruptedException` and then create finished food/call listeners; fetching also continues after interruption. The basket consumer catches an interrupted `take()` and continues the loop. Cancellation must exit those paths as well as shut down executors.

### P1 — compilation fails before tests can run

On 2026-10-01, `gradlew.bat testDebugUnitTest --console=plain --no-daemon` from `android/` failed in `:app:compileDebugJavaWithJavac` with **94 errors**, including missing `R.id`, `R.layout`, `R.string`, `R.raw`, and individual drawable symbols. This reproduces the September build symptom. No tests ran, and the precise resource-generation root cause has not been diagnosed. Restore compilation before relying on tests or device smoke checks.

Evidence: the test-task output and [development.md](../development.md).

### P1 — saving a finished pot consumes its food

[GameActivity.java:270–281](../../android/app/src/main/java/com/game/cookingspree/GameActivity.java#L270) calls `pot.getFood()` while serializing a DONE pot. [Pot.java:219–220](../../android/app/src/main/java/com/game/cookingspree/Pot.java#L219) delegates to [PotFunctions.java:47–56](../../android/app/src/main/java/com/game/cookingspree/PotFunctions.java#L47), which clears `foodDone` before returning it. After saving and resuming, the pot remains DONE but cannot yield food. Saving again dereferences null and fails. Use a non-consuming snapshot accessor for serialization. This is a normal save-path defect, independent of malformed input.

### P1 — a loaded cooking pot cannot be reliably saved again

[GameActivity.java:503–513](../../android/app/src/main/java/com/game/cookingspree/GameActivity.java#L503) restores progress and calls `restartCooking`. [PotFunctions.java:119–142](../../android/app/src/main/java/com/game/cookingspree/PotFunctions.java#L119) never assigns `recipeCooking`. Saving before that cooking finishes reaches `beingCooked.getName()` at `GameActivity.java:288` with null. The initial cooking path also publishes COOKING before its worker initializes `cookProgress`/`recipeCooking`, creating a separate save race. Read all cooking state through one consistent snapshot.

### P1 — tutorial creates an orphaned running game manager

[TutorialActivity.java:24–58](../../android/app/src/main/java/com/game/cookingspree/TutorialActivity.java#L24) calls `super.onCreate`, which initializes and starts a game, then replaces the layout and initializes another game without stopping the first. The old manager's handlers continue spawning and expiring orders and calling the same activity listener; they can invoke game over while the visible tutorial is paused, and persist after destruction because cleanup only sees the replacement manager. Workers are also replaced without cleanup. Compose the game once or explicitly dispose the first instance before replacement.

### P2 — tutorial pause setup runs before its steps exist

[TutorialActivity.java:235–245](../../android/app/src/main/java/com/game/cookingspree/TutorialActivity.java#L235) evaluates `tutorialSteps.size()` during both superclass setup and reinitialization, before `initializeTutorial` builds the list. It catches the null dereference, leaving the pause button visible. Inherited `GameActivity.onResume` also resumes the current manager after tutorial initialization paused it, so the initial walkthrough pause is undone. These are separate tutorial setup defects beyond the orphaned manager.

### P2 — pause does not freeze cooking, fetching, or player updates

[GameActivity.java:116–125](../../android/app/src/main/java/com/game/cookingspree/GameActivity.java#L116) pauses only `GameManager`. Cooking/fetch workers continue their sleeps and state changes, while [Game.java:204–205](../../android/app/src/main/java/com/game/cookingspree/Game.java#L204) continues updating the player. Lifecycle `onResume` unconditionally resumes the manager even if the pause menu is still displayed. Thus orders can resume behind the menu after returning from the background. A shared pause policy should cover gameplay state, worker progress and UI state; manual pause must survive lifecycle transitions.

### P2 — saved games omit basket selection and streak state

`saveGameState`/`loadGameState` (`GameActivity.java:150–526`) contain no serialization for basket contents or the fetcher's used/available ingredient lists. `initializeGameComponents` instead generates a random set before loading (`GameActivity.java:542`, `617–619`, `681–682`). Loading can therefore change the ingredients available for existing orders. The gameplay streak count and timing are also absent, so subsequent scoring changes after reload. Decide explicitly whether pending fetch progress and streak timing must survive saves.

### P2 — held-direction callbacks lack lifecycle cleanup

[GameActivity.java:569–590](../../android/app/src/main/java/com/game/cookingspree/GameActivity.java#L569) schedules a self-reposting movement runnable. Cleanup occurs only on touch UP/CANCEL; `onPause` and `onDestroy` never clear it. A second simultaneous direction DOWN replaces the sole `moveRunnable` reference while the previous runnable can remain queued, so releasing touches can leave an untracked movement loop. Cancel all movement callbacks and reset player movement at lifecycle boundaries, and track simultaneous touches deliberately.

### P2 — account queries disagree with the document schema

[AccountManager.java:189–203](../../android/app/src/main/java/com/game/cookingspree/AccountManager.java#L189) stores chef code and UID inside `profile`. Code generation and following query top-level `chefCode` at lines 248 and 346, and following reads top-level `uid` at line 351. For documents created by this implementation, uniqueness checks miss existing codes and follows cannot locate the intended profile. The check-then-create uniqueness algorithm is also non-atomic even after correcting the field path. `following` is written as an array although the schema comment describes a map; settle that contract alongside the queries.

### P2 — cloud settings hydration rejects non-Float numeric scale values

[AccountManager.java:308](../../android/app/src/main/java/com/game/cookingspree/AccountManager.java#L308) accepts joystick scale only if it is a Java `Float`, unlike volume and stats, which accept `Number`. A numeric value represented as `Double` or `Long` is silently ignored. Accept and validate `Number.floatValue()` consistently. Cloud hydration also uses syncing preference setters, causing redundant writes back to Firestore; local-only hydration would avoid that coupling.

### P2 — static preferences retain the latest activity

[PrefsHelper.java:17–22](../../android/app/src/main/java/com/game/cookingspree/util/PrefsHelper.java#L17) stores an `AccountManager` in a static field. [AccountManager.java:41–48](../../android/app/src/main/java/com/game/cookingspree/AccountManager.java#L41) retains its Activity, and destruction does not clear the reference. The most recently initialized activity remains reachable after it is destroyed until another initialization or process death, even without queued worker tasks. Separate persistent sync ownership from activity-bound credential/dialog UI.

### P2 — `GameView` has an unsafe render-thread stop protocol

The UI thread writes `isRunning` while the render thread reads it, but the field is neither `volatile` nor guarded. `surfaceDestroyed()` then performs an unbounded `join()` on the UI thread and does not interrupt the sleeping thread. This can cause a stale read or a UI stall during surface teardown.

Evidence: [`GameView.java`](../../android/app/src/main/java/com/game/cookingspree/GameView.java) lines 16, 30–52.

Use `volatile` (or an `AtomicBoolean`), copy and null-check the thread reference, call `interrupt()` before a bounded join, restore the interrupt flag if interrupted, and make the loop exit promptly. Consider using Android's frame scheduling instead of a manual sleep loop if rendering logic grows.

### P2 — save loading trusts persisted values without a compatibility boundary

`GameActivity.loadGameState()` reconstructs ingredients, pot states, and recipes directly from raw preference values, mutating live state before validation completes. Unknown pot states throw through `State.valueOf`; the broad catch only logs the exception, leaving a partially restored game. Other invalid values silently change the result: unknown ingredient IDs become `invalid` placeholders, unknown order recipe names are skipped, and unknown cooking recipes become `Waste`. Map-object reordering can silently assign saved contents to different objects. The format is unversioned and position-dependent.

Evidence: [`GameActivity.java`](../../android/app/src/main/java/com/game/cookingspree/GameActivity.java) lines 314–526 and [`docs/persistence.md`](../persistence.md).

Introduce a versioned `GameSave` DTO plus a validation/migration step that completes before mutating live game state. Use stable map object IDs rather than list positions and reject/clear an invalid save atomically with a user-visible recovery message. Cover old, malformed, unknown-recipe, and reordered-map saves with unit tests.

### P2 — recipe catalogue construction and identity are scattered

`Recipe.getDefaultRecipes()` makes new recipe objects with random UUIDs on every call. Callers independently rebuild it in game composition, save loading, submission, and other flows, while saves identify recipes by display name. `name` is final on each object, but changing authored names between app versions breaks compatibility. Current matching uses names and ingredient counts, not the random UUID, so UUID generation is not itself a demonstrated current matching bug.

Evidence: [`Recipe.java`](../../android/app/src/main/java/com/game/cookingspree/Recipe.java) lines 19–63; [`GameActivity.java`](../../android/app/src/main/java/com/game/cookingspree/GameActivity.java) lines 415, 488, and 531; [`SubmissionZone.java`](../../android/app/src/main/java/com/game/cookingspree/SubmissionZone.java).

Use stable authored recipe IDs at persistence boundaries. One possible design is an immutable `RecipeCatalog` shared by game systems; catalogue injection is a design proposal, not a prerequisite for fixing the demonstrated bugs.

### P3 — tests do not protect gameplay behavior

The only local unit test asserts `2 + 2 == 4`, and the instrumented test expects a stale, incorrect package name. The separate P1 build blocker prevents running these tests.

Evidence: [`ExampleUnitTest.java`](../../android/app/src/test/java/com/game/cookingspree/ExampleUnitTest.java), [`ExampleInstrumentedTest.java`](../../android/app/src/androidTest/java/com/game/cookingspree/ExampleInstrumentedTest.java), and [`docs/development.md`](../development.md).

First restore the build, then replace starter tests with focused tests for recipe matching (including duplicates), order expiry/streak rules, save validation/migration, and signed-out preference changes. Add an emulator smoke test for pause/resume and game lifecycle cleanup.

### P3 — small clarity and maintainability improvements

- In [`GameManager.java`](../../android/app/src/main/java/com/game/cookingspree/GameManager.java) line 104, replace boolean `&` with short-circuit `&&` for clarity. Both operands are side-effect-free booleans, so this is not a demonstrated behavior defect.
- Move map tile size, map dimensions, tileset names, and interactable property keys behind one map configuration/adapter. They are currently coupled across the Tiled file, `Game`, and rendering code.
- Remove broad exception swallowing around normal control flow. Catch expected parsing/state errors at the boundary, include actionable context, and preserve a safe, known state.
- Reduce debug logging from per-frame/per-interaction paths; it obscures real failures and adds avoidable work to gameplay.

## Codex Security Standard scan

Historical status recorded on 2026-09-21: completed, with partial repository coverage. The 2026-10-01 source review confirms the logging call sites below but does not independently verify the external scan's completion or coverage.

The audit concentrated on Android component exposure, Google/Firebase authentication and Firestore client behavior, local profile/save storage, bundled asset parsing, and backup configuration. It reviewed the current directory snapshot without executing the app or contacting external services.

One low-severity finding was validated:

### Low — profile preferences are written verbatim to Logcat (CWE-532)

[`MainActivity.java`](../../android/app/src/main/java/com/game/cookingspree/MainActivity.java) obtains every entry in `chef_prefs` and logs each key and value during activity creation. The store includes player-facing profile data such as chef name, chef code, and photo URL via [`PrefsHelper.java`](../../android/app/src/main/java/com/game/cookingspree/util/PrefsHelper.java).

This does not give ordinary third-party apps automatic access to the data—the manifest does not request `READ_LOGS`—but it unnecessarily exposes profile data to diagnostic, privileged, or locally collected logs.

Recommended remediation: remove the dump and the additional chef-name logging in `MainActivity.java:166` and `AccountManager.java:275`. Removing only the dump leaves profile data logging in place. If diagnostics are needed, use debug-only, redacted status messages and never emit raw preference values or identifiers.

## Reviewed controls and follow-up questions

- The launcher is the only exported activity; `GameActivity` and `TutorialActivity` are non-exported.
- No remote asset importer or externally controlled asset path was established; map and sprites are read from bundled app assets.
- The Firestore client selects documents using the current Firebase user ID, but Firestore Security Rules are external to this repository. Review those rules before treating client-side UID construction as an authorization guarantee.
- Backup and device-transfer XML files are templates without an explicit policy for `chef_prefs` or `GameSave`. The application manifest does not reference either `fullBackupContent` or `dataExtractionRules`, so editing the XML files alone would not connect that policy to the app. Confirm the intended backup/transfer treatment before release; device behavior was not tested.

## Coverage

Coverage is partial. The audit reviewed the application security surfaces above; it did not inspect every path in the 2,127-item directory snapshot, particularly source-art, map-authoring, historical-log, and planning directories. Generated Gradle caches and build outputs were excluded.

The original report states that a canonical scan bundle was generated by Codex Security outside the workspace, with one low-severity finding. That bundle was not available for this validation.

## Verification and limits

Ran `gradlew.bat testDebugUnitTest --console=plain --no-daemon` from `android/`. It failed in `:app:compileDebugJavaWithJavac` with **94 errors**, including missing `R.id`, `R.layout`, `R.string`, `R.raw`, and individual drawable symbols. No unit tests ran. This reproduces the September report's build symptom; the precise resource-generation root cause was not diagnosed here.

The findings above are based on direct source/control-flow inspection. Runtime manifestations, device lifecycle behavior, and backend requests were not executed. No emulator test or external Firebase verification was performed. Source findings are not a claim of exhaustive repository/security coverage. No application code was changed.
