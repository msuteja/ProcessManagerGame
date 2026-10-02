# Visual asset workflow

Purpose: use Gemini-created artwork efficiently while preserving provenance and making integration reproducible. The product owner generates assets through their Gemini subscription unless a future connected tool changes that arrangement.

## Asset brief required before generation

An agent requesting an asset supplies:

- Purpose and the game screen/object it serves.
- Target path, file format, pixel dimensions, transparency requirement, and required state/variant names.
- Art direction and references; distinguish inspiration from assets that may be copied.
- A ready-to-paste Gemini prompt, including negative constraints where useful.
- Integration and validation steps.

The product owner provides the chosen generated file. Store its source prompt, generator, generation date, any references, usage/licensing review, and final runtime/source paths in an asset register when the asset is accepted. Update the relevant map/resource documentation when asset names, states, or paths change.

## Current locations

- Source artwork: `new sprites/`.
- Runtime canvas tiles: `android/app/src/main/assets/tiles/`.
- Android UI drawables: `android/app/src/main/res/drawable/`.

Do not overwrite a current asset without retaining a recoverable source or receiving explicit product-owner approval.
