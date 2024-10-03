# Tactic AI

## Domain knowledge

1. Corner kicks are set pieces
2. Set pieces are relatively fixed starting points, ie: $s_0$
3. Corner kicks are nice because they are *frequent, predictable, short horizon*

## What is it?

An AI model that can
1. predict outcomes of outcomes of corner kicks
2. Cluster past demonstrations for retrieval
3. Generate recommendations to have better performance

## How is it tested

1. Receiver prediction - who first receives the ball
2. Shot prediction -
3. Tactic recommendation -

## Technologies used

1. Graph representation + neural networks
2. Geometric DL for geometry invariance

## Easy improvements

### Better features (not very applicable, they probably tried a lot):

1. Ball information as feature
2. Player profiles

### Performance

1. Receiving Player Prediction
   - 22 players, select top 3, 78.2% prediction accuracy
  
## Architecture

1. Controlled Refinement
   - Is it not possible to just use gradient descent to perturb player locations? Or some form of diffusion process using the gradients?
   - Can then easily control amount of improvement