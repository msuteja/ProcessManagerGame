# Idea inbox

Purpose: append-only capture of uncommitted Cooking Spree inspiration. Entries copied from the original future-plans file on 2026-09-21 preserve its detail; the original file remains unchanged.

## Account, local play, and cloud save

**Source:** original plan, “Matthew Prio”.

- Make the game playable without Google sign-in.
- High score records locally when not signed in; when signed in it updates both local storage and Firebase.
- If a player signs in later, ask whether to sync current records. The original idea contemplated overwriting with Google data if they decline and showing basic account data such as chef name.
- Make the game playable without internet. Social features—leaderboard, cloud save, friends—are disabled offline and enabled when internet is available.
- The device is the source of truth while offline. Records are saved locally; when online and previously signed in, sync with Firebase.

**Direction link:** accepted cloud-profile rules in [../direction.md](../direction.md).

## Competition, account data, and friends

**Source:** original plan, “Code”.

- Add a leaderboard.
- Decide what the Google account contains. The original notes expected Firebase-to-preference sync at first sign-in and on `MainActivity.onCreate`.
- Add friends by UID or Chef Code; add a friends leaderboard.
- Split single-player and multiplayer leaderboards; multiplayer would show total score and involved people.

**Direction link:** first global leaderboard, then Chef-Code friends ranking, are accepted; multiplayer leaderboard details remain uncommitted.

## Preference migration and account validation

**Source:** original plan, “Code”.

- Settle preferences migration and remove obsolete commented code after it works reliably.
- Chef name previously had intermittent issues; volume sync was reported working; stats sync was reported working locally but Firebase sync was not verified.
- Joystick selection did not appear checked inside the game. Consider storing joystick size as `small`/`large` rather than a float to avoid precision issues.
- Test profile, stats, and settings sync.
- Firebase setup was completed historically but later noted as expired/unverified.

## Game quality and tutorial

**Source:** original plan.

- Improve the tutorial into one that forces/validates player actions.
- Ensure a completed loaded game cannot be loaded again to farm high scores.
- Make the map work across device dimensions.
- Modify difficulty: consider basing timers on time played or score so newer orders do not expire faster and difficulty rises over time.
- Add a 3-second animation while ingredient boxes change; possible theme is a monkey/robot moving boxes, as a non-interactable path blocker.

## Social and multiplayer concepts

**Source:** original plan.

- Multiplayer where different people are responsible for different ingredients.
- A catapult might send items to other screens; participants may need to sit in order to establish left/right positions.

**Direction link:** co-located party multiplayer is accepted as a future outcome; all technical and game-rule details remain open.

## Economy and customisation concepts

**Source:** original plan.

- Publish to Google Play.
- Potential monetisation/customisation: object/clothing skins, map themes such as Christmas/CNY, power-ups (speed, pot boost, timer freeze, order wipe, undo failure), and a coin shop with game-earned coins.
- Determine what uses coins versus real money and whether passive income is appropriate.

**Direction link:** monetisation, currencies, skins, and power-ups are out of scope. Preserve these ideas for a future owner decision.

## Design, content, and release concepts

**Source:** original plan plus accepted direction.

- Settle music and images and ensure they are appropriately usable/licensed.
- Fix icons and create cohesive themed artwork; the original notes that some food names/icons do not match and the onion looks odd.
- Add creators/credits and GitHub links.
- Gemini may be used for sprites, wallpapers, and related artwork through a product-owner handoff. See [../assets.md](../assets.md).

## Existing completed or historical notes

**Source:** original plan.

- Database/Firebase setup, rubbish-bin Easter egg, wording change from “processes” to “orders”, and credits were marked complete historically.
- The rubbish-bin Easter egg says: “Stop playing with my feelings, give me some actual food!” after repeated empty interactions.

These are historical notes, not guarantees that the current implementation is correct.
