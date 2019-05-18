# Simple Game

## What

Simple Game is a Python script that simulates an entity within a map that tries to find the WINNING cell through the strategy of Reinforcement Learning.
The entity starts off with no idea of where the WINNING cell is located within the map. It tries any possible route until it starts learning the best (shortest) path to reach the WINNING cell.

## How

The map is 11x11 cells and composed of walls, 4 LOSING cells and 1 WINNING cell. At each step, the entity is able to go towards 4 directions: North, South, East or West, unless there is either a wall or the edge of the map.
-	In case the entity falls into a LOSING cell, the game ends and the entity restarts from the center of the map;
-	In case the WINNING cell is reached, the game ends and the entity restarts from the center of the map;
-	The final goal is to reach the WINNING cell through the shortest path;
-	Any time the game restarts due to either winning or losing the game, the entity knows a bit more about the composition of the map and is therefore able to reach the WINNING cell by using the knowledge acquired during its past experience.

## Try it !

`python3 Learner.py`

## Watch it !

coming soon
