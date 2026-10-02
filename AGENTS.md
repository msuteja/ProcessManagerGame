# Cooking Spree — agent guide

## Read just enough

1. Start with [docs/README.md](docs/README.md). It routes each kind of task to a small, purpose-built document.
2. For an implementation task, read the relevant document(s), then the named source files. Treat source and Gradle configuration as authoritative when they disagree with prose.
3. Do not recursively load assets, generated Gradle files, or the historical logs unless the task requires them.

## Delivery loop

For implementation work, Terra and Sol are orchestrators: make a detailed, reviewable plan before coding. When the user has asked to be grilled or a plan already resulted from that discussion, use that agreed plan as the implementation contract. Hand the bounded implementation task, acceptance criteria, and affected files to a Luna agent. The orchestrator reviews the result, integrates it, runs the relevant checks, and reports evidence.

For a small direct fix where delegation would cost more than it saves, state that judgment and proceed. Do not delegate a task whose safety or product decision still needs the user's answer.

## Product and platform direction

Cooking Spree serves casual gamers: it must remain enjoyable offline as a single-player game, with optional online leaderboard competition. The intended future social mode is in-person multiplayer for gatherings, in the spirit of a party cooking game. Android is the only active platform. Start iPhone work only after the user has established and accepted an Android-completion roadmap; do not let prospective iPhone support expand current Android tasks. Read [docs/product.md](docs/product.md) for product decisions.

Read [docs/direction.md](docs/direction.md) before proposing product work and [plans/android-roadmap.md](plans/android-roadmap.md) before planning execution. Preserve every uncommitted inspiration in [docs/ideas/inbox.md](docs/ideas/inbox.md); agents may connect or refine an idea but only the user can promote it into committed roadmap work.

## Change, commit, and PR contract

Agents may commit and open pull requests. Use [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/) for commit messages. A PR must contain a clear changelog, the reason for the change, material decisions/trade-offs, verification evidence, and the associated documentation updates. The user reviews every PR and has final approval; opening a PR never authorizes a merge.

Every PR, in every phase and for work outside the roadmap, must include evidence that makes the changed behavior reviewable without checking out the branch. For visual or scene changes, attach labelled screenshots of every materially changed scene and include before/after views when they clarify the difference. For interaction, animation, or lifecycle behavior that a still image cannot prove, attach a short screen recording or a concise sequence of screenshots. Non-visual changes still require relevant command/test results. State the device or emulator and API level used for Android evidence, identify any scenario that was not verified, and redact account details, tokens, Firebase configuration, and other sensitive data from all evidence.

Use `.github/pull_request_template.md` when opening a PR.

For a decision that changes architecture, persistence/data compatibility, platform scope, multiplayer/online approach, or a product commitment, create a concise ADR under `docs/decisions/` before or alongside implementation. The user decides whether it is accepted. See [docs/decisions/README.md](docs/decisions/README.md).

## Documentation is part of the change

Every behavior, architecture, build, test, persistence, map, asset, or workflow change has a documentation owner: the agent making the change. Before handoff, update the applicable file under `docs/` and its links/index if the navigation changed. Remove or correct superseded claims; do not append a stale historical layer. See [docs/doc-maintenance.md](docs/doc-maintenance.md).

## Project boundaries

- This is a native Android Java game in `android/`; run Gradle commands from that directory.
- Preserve `android/app/google-services.json`; it is Firebase configuration. Do not reproduce its contents in documentation, logs, or commits.
- `Tiled stuff/` is map-authoring source; `android/app/src/main/assets/map.tmj` is the runtime map. Keep them deliberately synchronised when map work is requested.
- `new sprites/` is source artwork; runtime map assets are in `android/app/src/main/assets/tiles/`, while Android UI artwork is in `android/app/src/main/res/drawable/`.
- This project is one Git repository rooted here. Run Git commands from this project root.
- Canonical project guidance and documentation live at this root under `AGENTS.md`, `docs/`, and `plans/`.

## Verification baseline

Use `gradlew.bat testDebugUnitTest` for local unit tests and `gradlew.bat connectedDebugAndroidTest` only with an attached emulator/device. Follow [docs/development.md](docs/development.md) for setup, build, manual smoke tests, and current limitations.
