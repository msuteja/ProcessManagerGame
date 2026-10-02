# ADR 0002: Offline-first competition direction

Status: Accepted
Date: 2026-09-21

## Context

Cooking Spree needs to serve casual players without requiring network access while also supporting cloud-backed competitive and social features. The existing plan mixed implementation ideas, competition, multiplayer, and monetisation without settled sequencing.

## Decision

Restore a buildable/testable Android baseline before feature work. Then prioritise optional Competition (cloud profile, global all-time leaderboard, Chef-Code friends ranking), followed by offline single-player quality and later co-located multiplayer. Guest/local play remains available without internet or an account. Cloud conflicts require the player to choose a complete local or cloud profile; active games remain device-local. Monetisation is out of scope. Android is the sole active platform until the product owner approves Android-roadmap completion.

## Consequences

Competition work must not gate offline play. The global leaderboard is delivered before friends ranking, though both are near-term priorities. Multiplatform, remote multiplayer, seasons, advanced anti-cheat, and monetisation cannot be smuggled into current work. Product inspirations are preserved in the inbox until explicitly promoted into roadmap work.
