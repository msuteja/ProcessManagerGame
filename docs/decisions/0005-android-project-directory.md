# ADR 0005: Android project directory

Status: Accepted
Date: 2026-10-03

## Context

The Android Gradle project directory was named `Code/`, which did not clearly identify its role and was unconventional for an Android project at this repository's root.

## Decision

Name the Android Gradle project directory `android/`. This is the clear, conventional project directory name for the repository. Gradle commands and all active documentation paths use `android/`.

## Consequences

The tracked project tree and documentation paths use `android/`. ADR 0004 remains the historical record of the accepted project-root repository decision and is not rewritten to conceal the earlier `Code/` layout.
