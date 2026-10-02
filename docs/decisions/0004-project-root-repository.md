# ADR 0004: Project-root repository

Status: Accepted
Date: 2026-10-03

## Context

The Android Gradle project was nested in its own Git repository while canonical guidance, documentation, plans, map-authoring files, and source artwork lived outside its versioned boundary. This split required duplicate documents and made repository changes hard to review with their project context.

## Decision

Use one Git repository at the project root. It contains Android `Code/`, canonical docs and plans, map-authoring sources, and source artwork. Keep Gradle commands rooted in `Code/`. Preserve the existing Git history without rewriting it; move the repository metadata structurally and record the layout change in a normal commit.

## Consequences

Project documentation and implementation can be reviewed and versioned together. Git commands run from the project root and Android build commands run from `Code/`. Runtime and authoring map files remain separate artifacts that must be deliberately kept in sync when map work changes them.
