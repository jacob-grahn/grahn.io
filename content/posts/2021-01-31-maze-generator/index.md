---
title: "Bubble Racing: Maze Generator"
date: "2021-01-31"
author: "Jacob Grahn"
description: "A multi-part series on the making of Bubble Racing"
---

Rather than taking the time to actually design interesting levels (or create a level editor), we're going to go with randomly generated mazes instead! It's amaaaze-ing!

If you're wondering how one goes about writing some maze-generating code, here's the secret: Do a search for "how to program *something*", and some useful results will turn up. I searched for "how to program _maze algorithm_". [This Wikipedia page](https://en.wikipedia.org/wiki/Maze_generation_algorithm) was one of the results, and it was both helpful and interesting.

## Setup

Create the following files and folders:
```
📂maze_generator
 ┣ 📜MazeGenerator.gd
```

Well, the structure of this one is pretty simple. The code is a bit complex though. If you're interested in looking through the code, go for it! Just copying and pasting it works fine, too. If you're into that kind of thing.

Here's the code for MazeGenerator.gd
```python
extends Node

# This is just a wrapper that calls 
# 'make_nodes' and then 'connect_nodes_randomly'
func generate(width, height):
	var node_list = make_nodes(width, height)
	connect_nodes_randomly(node_list)
	return node_list
	
# Creates bunch of nodes with no connections
func make_nodes(width, height):
	var node_list = []
	for x in range(width):
		for y in range(height):
			var node = { 'position': Vector2(x, y), 'connections': [] }
			node_list.push_back(node)
	return node_list

# Open up connections between nodes
func connect_nodes_randomly(node_list):
	var unused_node_list = node_list.duplicate()
	unused_node_list.shuffle()
	var stack = [unused_node_list.pop_back()]
	while unused_node_list.size() > 0:
		var node = stack[stack.size() - 1]
		var moved_forward = false
		for other_node in unused_node_list:
			var dist_x = other_node.position.x - node.position.x
			var dist_y = other_node.position.y - node.position.y
			var dist = sqrt((dist_x * dist_x) + (dist_y * dist_y))
			if dist <= 1:
				stack.push_back(other_node)
				unused_node_list.erase(other_node)
				node.connections.push_back(other_node.position)
				other_node.connections.push_back(node.position)
				moved_forward = true
				break
		if !moved_forward:
			stack.pop_back()
```

<iframe width="560" height="315" sandbox="allow-same-origin allow-scripts allow-popups" src="https://tube.jigg.io/videos/embed/c20e3fe1-3d6d-4f4c-940a-0925a98cbaa8" frameborder="0" allowfullscreen></iframe>


## Test
Create the following files and folders:

```
📂maze_generator
 ┣ 📜MazeGenerator.gd
 ┣ 📜MazeGeneratorTest.gd
 ┣ 📜MazeGeneratorTest.tscn
```

Here's the code for `MazeGeneratorTest.gd`
```python
extends Node2D

# Generate a maze
var MazeGenerator = preload("../maze_generator/MazeGenerator.gd")
var maze_generator = MazeGenerator.new()
var maze_width = 50
var maze_height = 50
var maze = maze_generator.generate(maze_width, maze_height)

# Draw the maze
var line_color = 0xFFFFFFFF
var line_width = 8
var maze_scale = 10
var offset = Vector2(20, 20)
func _draw():
	for node in maze:
		for connection in node.connections:
			draw_line(
				node.position * maze_scale + offset,
				connection * maze_scale + offset,
				line_color,
				line_width
			)
```

Attach `MazeGeneratorTest.gd` to the root node of `MazeGeneratorTest.tscn`. Hit the `Play Scene` button to test out your amazing code.

<iframe width="560" height="315" sandbox="allow-same-origin allow-scripts allow-popups" src="https://tube.jigg.io/videos/embed/2c7845f7-5079-4c22-9c53-317529b72785" frameborder="0" allowfullscreen></iframe>