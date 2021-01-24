---
title: "Bubble Racing: Legend of the Seeker"
date: "2021-01-24"
author: "Jacob Grahn"
description: "A multi-part series on the making of Bubble Racing"
---
![legend of the seeker tv show](legend-of-the-seeker.jpg)

Err, ok. Not that seeker. (Did anyone else watch that show?)

This seeker will be a hidden helper within our game. It tries to get to a `target_position`, but it will be stopped by `walls` and other such things. The behavior will come in handy, and we'll use it later to make `bubbles` and `fish`.

## Make the Seeker class

So really the Seeker is all about the code. But we will give the seeker a face regardless, mostly because it will make the test in the next section look better. Here comes a numbered list!

1. Make a new folder called `seeker`
2. Make a new Scene named `Seeker`, and save it in the `seeker` folder.
4. Pick 2DNode to start with, but you'll have to change to root node to `RigidPhysicsBody`
5. Download this image of a charming broomstick riding individual from above and move it to the `seeker` folder. ![stick figure riding a broomstick](seeker.png)
6. Drag the charming broomstick riding individual into your `Seeker` scene.
7. Click on the funny little parchment paper on the top left. It's the one with a green x.
8. Go with the default options, but save the new script in the `seeker` folder.
9. Copy and paste this fine code:
```
extends RigidBody2D

# Affects how long it takes for an object to go from 0 to max velocity
# Larger number = faster acceleration
var acceleration = 30

# This is the force of friction, which is always trying to bring an 
# object to a stop. Larger number = more friction
var linearDamp = 3

# Once the seeker is within this X pixels of the target, it starts to slow down
# to avoid overshooting the target_position
var throttle_dist = 150

## The x,y coordinates that the seeker should be trying to reach
var target_position = Vector2(0 ,0)

# Called when the node enters the scene tree for the first time.
func _ready():
	set_linear_damp(linearDamp)
	target_position = position

# Move towards target position
func _process(_delta):
	var vector = (target_position - position).normalized()
	var dist = target_position.distance_to(position)
	var throttle = min(dist, throttle_dist) / throttle_dist
	var impulse = vector * acceleration * throttle
	apply_central_impulse(impulse)
```

<iframe width="560" height="315" sandbox="allow-same-origin allow-scripts allow-popups" src="https://tube.jigg.io/videos/embed/f92915dc-807a-4004-9447-caa19fef4a47" frameborder="0" allowfullscreen></iframe>

## Test it Out

To test our seeker out, we're going to build a little obsticle course. Then we're going to tell the seeker to fly around completely at random. Nice.

1. Create a new Scened named `SeekerTest`. Save it in the `seeker` folder.
2. Drag some `walls` into the scene. We can't let those walls be slacking off, now can we?
3. Drag a `Seeker` into the scene.
4. Create a new script for SeekerTest and copy and paste this script:
```
extends Node2D

var rng = RandomNumberGenerator.new()

func _on_Timer_timeout():
	var x = rng.randi_range(100, 400)
	var y = rng.randi_range(100, 400)
	$Seeker.target_position = Vector2(x, y)
```

5. Add a Timer to the scene, and set the timer to automatically start.
6. Link the timer's `timeout` signal to the SeekerTest script. This will run the code in that timeout handler once per second (by default).
7. Hit the `Play Scene` button on the top right to send our charming broomstick riding individual flying.
8. Congratulations! You now have done a thing.

<iframe width="560" height="315" sandbox="allow-same-origin allow-scripts allow-popups" src="https://tube.jigg.io/videos/embed/13281bd5-30d0-4514-b73f-eb415fc12262" frameborder="0" allowfullscreen></iframe>