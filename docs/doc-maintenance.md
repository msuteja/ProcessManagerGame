# Documentation maintenance

Purpose: keep this documentation accurate without creating a parallel, bloated copy of the codebase.

## Before handoff

1. Identify the changed contract: user-visible behavior, architecture, build/test command, persistence schema, map/asset convention, or workflow.
2. Update the one documentation page that owns that contract; keep facts in a single source here.
3. Update `docs/README.md` only if navigation or a document purpose changed.
4. Remove or revise conflicting statements instead of adding a dated workaround.
5. In the handoff, name the documentation file changed and the verification performed.

## Ownership map

| Change | Update |
| --- | --- |
| Gameplay rule, recipes, scoring, interaction, tutorial claim | `gameplay.md` |
| Activity wiring, engine, map, assets, threads, structural seam | `architecture.md` |
| Build, SDK/toolchain, commands, test suite, manual test procedure | `development.md` |
| Saves, preferences, sign-in, Firebase, cloud schema | `persistence.md` |
| Product priority, accepted limitation, planned feature | `roadmap.md` |
| Agent workflow or repository guardrail | `AGENTS.md` |

Write links to source files rather than duplicating long APIs or configurations. If a claim cannot be kept current, delete it or replace it with a pointer to its source of truth.
