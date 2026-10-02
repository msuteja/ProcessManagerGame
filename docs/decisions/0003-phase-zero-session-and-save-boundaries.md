# ADR 0003: Phase 0 session and save boundaries

Status: Proposed
Date: 2026-10-01

## Context

The current offline baseline has duplicate tutorial sessions, unfinished worker teardown, inconsistent pause behavior, destructive pot saves, and partially applied legacy loads. Baseline recovery needs explicit behavior without expanding into a new engine, cloud feature, or save migration project.

## Decision

Propose one activity-owned gameplay session with idempotent teardown and listeners that cannot mutate closed sessions. Preference storage/cloud-update ownership must not statically retain an Activity; credential UI remains activity-owned.

Track manual, background, and tutorial pause reasons separately. Any active reason freezes gameplay timers, movement/input, cooking, and ingredient exchange progress. Foregrounding removes only the background reason. Resume continues in-memory work; game over is terminal and finalizes statistics/save clearing once. The tutorial may explicitly permit its existing movement demonstration.

Preserve existing valid legacy save keys and the current map/catalogue. Capture non-consuming, consistent snapshots at pause, including a stable logical player tile. Validate a complete candidate before applying it or starting workers. For legacy fractional player coordinates, normalize only to a valid nearby traversable tile or reject safely. Failed saves preserve the previous save. Invalid/completed loads show recovery without partially restoring, awarding statistics, or silently deleting stored data.

Do not add versioned schemas, stable map/recipe IDs, basket/fetch/streak continuity, or cloud saves in Phase 0. Record those limitations for later planning.

## Consequences

The owner must accept these proposed behavior/architecture boundaries before dependent implementation. Small lifecycle, timing, sync, and snapshot seams are permitted; wholesale game-engine replacement is not. Pause becomes consistent across systems, and valid legacy saves remain readable within the current map/catalogue. Future map/catalogue changes and complete save continuity still require a separate compatibility decision.

Implementation slices and verification criteria are in [the Phase 0 plan](../../plans/phase-0-baseline-recovery.md), linked from [the overarching roadmap](../../plans/android-roadmap.md). Phase 0 completion requires automated and device evidence plus owner PR approval.
