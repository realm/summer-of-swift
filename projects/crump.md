# Crump

## Description

Crump is an arcade game with 80s style play based around OS X, keyboard input, tmx (Tiled) tile maps, and SpriteKit.  The exact details of the gameplay are still a work in progress but enemy avoidance, constant movement withn a grid, and collection will be its essential elements.

## Project Location

https://github.com/davecom/Crump

## Team Members

- David Kopec — [@davecom](https://github.com/davecom)

## Updates


### July 9

I've gotten the open source JSTileMap library to work with Swift using Obj-C interop.  I'm using it to read .tmx Tile maps created with the open source editor Tiled.  I also discovered, along with the author of the JSTileMap library, a bug in Apple's PNG library on OS X that doesn't exist on iOS. It is related to the size of a particular PNG being miscalculated. For more details, you can read through this issue:
https://github.com/slycrel/JSTileMap/issues/11

I have started a basic level and have learned to use Tiled along with JSTileMap.

### July 23

I implemented basic movement for the player within the environment and basic key mapping. The concept is that the player keeps moving until he hits a wall (barrier) of some sort.  For now, you can try out a testing version with the keys "wasd" for up, left, down, right respectively. The player has to stay on a grid, so there are several waypoints along the way that I'm calling "decision points." A decision point is a place where the player could go in a different direction. If you press a key for a different direction before the player reaches a decision point, and that direction is available at the decision point (there's not a wall/barrier blocking it) then the player will change directions.  The player can always reverse direction. A key function is findDecisionPoint() - I should work on optimizing it since enemy AIs will likely need to use it too.

I'm looking for a good Cocoa control to use for user keyboard preferences (mapping which key goes to which direction). I looked at OpenEmu and they're just using text boxes - I've seen full keyboard layouts with drag and drop labels before in Mac games - I need to remember which ones and figure out what controls they're using or if they're doing it all manually.

I need to create/find some better public domain art (sprite sheets really for Tiled) and start working on some enemies/goals/things to pick up for points. I have some basic ideas in my mind but I need to put them to paper.

### August 4

I added some basic AI and hooks for more AI.  I had to slightly refactor some of the code.  The smart bit was putting the player movement code at the base of a class hiearchy that incorporates the AI characters (all of that decision point stuff is applicable to everything that moves in the game).

I'm going to spend some time working on the art and future AI characters.

### August 18

I haven't spent much time on Crump the last couple of weeks unfortunately beyond starting work on some graphics.

### August 25

### September 10

### September 25
