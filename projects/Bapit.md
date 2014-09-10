# Bapit

## Description

Bapit is going to be a SpriteKit game written in Swift.  It's a take on the classic game where you must tap a ball to keep it up in the air for as long as possible.

## Project Location

https://github.com/hswolff/Bapit

## Team Members

- Harry Wolff [@hswolff](https://twitter.com/hswolff)

## Updates

(The dates below are the minimal dates suggested. Please feel free to update earlier & more often!)

### July 25

The project has begun!

I've created the initial game scaffold using Xcode's new Sprite Kit project layout.

Basic gameplay is functional.  You can tap only on the ball to cause it to move upwards in the scene.

We're also calculating where inside the ball you touched and using that when calculating the impulse vector to apply to the ball.

Also moving away from the .sks file and towards pure code implemenation of the game.

### August 10

This creates my first enum and struct.

The enum is for PhysicsBody category and collision bit mask, to allow
me to handle contacts between two physics bodies correctly.

The struct is to create a data type for storing the amount of taps
that someone correctly tapped the ball, and someone missed it in case
I'd want to use that information.

Also moved to instantiating each property in the initializer function init().

Also sped up the game to make it be a little more challenging.

Created the bottomBorder to know when the ball drops to the bottom.

Created scoreLabel to show the current score of the game.

Used the willSet observer to get notified when the TapCount struct is
modified so we can then update the scoreLabel correctly.

Add an extension to GameScene so we can become a contactDelegate
and handle all contact instances.

Abstract the setting up of the game kit view and initial scene
so it's the same no matter how the game is created.

Add ability to save the high score for that session of the game.

Still using observing functions to make for easy binding and updating
of the view.

Created GameOverScene to present once the ball hits the bottom of the screen.

Using SKTransition when presenting each scene for an added touch of life.

Added a 'tap to start label' so that the game only starts once the player
is ready.  I'm controlling this behavior by changing the dynamic property
of the ball's physicsBody.

**Sidenote**:  I'm really enjoying Swift's willSet/didSet property observer methods
on properties.  Really makes databinding really easy and lightweight to use.  Big
props to Apple for that.

### August 25

Improve the mechanism by which we have a 'resting' state of the game.

Previously we were changing whether the ball's physicsBody's dynamic
property was true or not.  This property controls whether it is effected
by the physics simulation in the page.  So, when it's false it doesn't
move at all.

However there was a performance hit when turning dynamic to true as
it required some initial processing to begin emulating how the physics
in the scene should behave.

As a result you could sometimes tap the ball too soon prompting there
to be a noticable lag in the game and make the overall game play not as
smooth as it should be.

Instead I'm taking the route of changing the physicsWorld.speed property
from 0 to the desired value.  This seems to add no performance hit
and accomplishes the same thing we wanted to happen.

Fixes for beta6.  The interfacing with obj-c APIs have changed a little.

Tweak animation when going from game scene to game over scene.

Created MainMenuScene as the first thing that people see when they open the app.

It has two buttons right now, one to play and one to go to settings (which
doesn't exist yet).

Updated the flow of the game to be a little more fluid.  Tweaked the
animations as well to make it feel more cohesive.

### September 10

Sadly I haven't had a lot of time to work on Bapit in the past two weeks.  The only work I've managed to do in this interim is to update my project to be compatible with beta 7.  Today I tried it with the GM and it also worked without edit.

The edits I had to do for beta7 were a little curious as they required adding `?` when accessing certain properties on an object.

For example on a SKScene when showing a new scene, before I could do:

```
view.presentScene(scene, transition: transition)
```

Whereas in beta 7 (and GM) I had to modify it to:

```
view?.presentScene(scene, transition: transition)
```

Little curious about the why to this change.

Aside from that it was an easy upgrade.

### September 25
