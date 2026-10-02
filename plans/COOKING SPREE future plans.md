# COOKING SPREE future plans / direction

# Matthew Prio

- [ ]  Make game playable without google sign in
- Logic:
    - high score will only record locally if not signed in, if signed in it will update both local and firebase (simple try catch, or if statement)
    - if afterwards they want to sign in, we ask them if they want to sync current records with Google account, if not it will be overriden with whatever was saved on Google account (could be empty game), might want to show basic data like chefs name (similar to how coc does it)
- [ ]  Allow game to be playable without internet connection (social features like leaderboard, cloud save, friend list disabled without internet, enabled when there's internet)
    - Logic:
        - the users device will be the source of truth
        - if offline, all records saved on device, once online and signed in before, auto syncs with firebase

# Code

- [ ]  Add leaderboard
    - [x]  decide on what exactly the google account will contain
        - check firebase for what is stored
        - firebase → prefs syncing happens when
            - signing in for the 1st time
            - loading the game (main activity’s on create)
            
- [ ]  Settle the prefs migration, delete commented code when the prefs working fine. As of 1 July:
    - the chefName had some issues, and idk how it fixed it just suddenly started appearing. need more testing. The rest loaded just fine
    - Volume is syncing fine from the main settings
    - stats syncing with prefs just fine, havent checked if syncing with firebase
    - joystick is not syncing properly within the game itself. The radio button is not appearing to be checked
    - change firebase to store a String for joystick size (small and large) cos storing float leads to floating point error
    - [ ]  Test if all the syncing works
        - [ ]  profile
        - [ ]  stats
        - [ ]  settings
- [ ]  Better tutorial (those kind where they force u to do something)
- [ ]  Friends system, add friends
    - [ ]  add by uid or chefcode
    - [ ]  leaderboard amongst friends
- [x]  setup database
    - [x]  firebase?
      - NOTE: since the time of implementation, the firebase has expired, and I have not checked up on it.
- [x]  Easter egg
    - [x]  Idea: rubbish bin, if u interact 10 times without throwing any food it says something like “stop playing with my feelings, give me some actual food”
- [x]  Add creators/credits, put GitHub links?
- [x]  Change wordings to match better e.g. all those processes change to orders
- [x]  Fix: after loading from save file and completing the game, shouldnt be able to load again, if not easy to spam high scores with a good save file




---
- [ ]  Publish to google play
- [ ]  Make the map work with devices of different dimensions
- [ ]  Modify game difficulty
    - [ ]  Idea: have the timer be based on time played / scored, so that won't have a case where a newer order expires faster, and also the game gets harder as u go on
- [ ]  How to earn money
    - [ ]  Skins for objects/ clothes for character
        - [ ]  Map theme? Christmas, CNY etc
    - [ ]  Power ups
        - [ ]  Idea: speed boost, pot boost, freeze timer, wipe orders, undo 1 failure
    - [ ]  Determine what can be bought with coins and what use real money (passive income)
    - Have a “coins” shop where coins are earned by playing the game, based on your score.
- [ ] multiplayer mode, different people are responsible for different ingredients, can use catapult to send to other screens. must sit in order to have left and right
- [ ] split single player and multiplayer leaderboard, multiplayer show a total score and the involved people


1. Make game playable without google sign in

Logic:
- high score will only record locally if not signed in, if signed in it will update both local and firebase (simple try catch, or if statement)
- if afterwards they want to sign in, we ask them if they want to sync current records with Google account, if not it will be overriden with whatever was saved on Google account (could be empty game), might want to show basic data like chefs name (similar to how coc does it)


2.  Allow game to be playable without internet connection (social features like leaderboard, cloud save, friend list disabled without internet, enabled when there's internet)
Logic
- the users device will be the source of truth
- if offline, all records saved on device, once online and signed in before, auto syncs with firebase


## Design 
- [ ]  Settle music and images, make sure not copyrightable
- [ ]  Fix the icons, make our own images with theme (find a designer)
    - [ ]  Some food name and icon don't match, the onion also looks weird
- [ ]  Have some kind of animation for the 3s where the ingredient boxes are being changed
    - [ ]  Idea: monkey / robot changing the boxes. The whole thing is a non interactable object that blocks the path