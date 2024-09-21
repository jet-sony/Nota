# Power of Planning

## Scaling Test Time Compute via MCTS

## Parables on the Power of Planning [$_1$](https://www.youtube.com/watch?v=eaAonE58sLU&t=2398s)

### AI in Poker

- 100x model size, 2x performance increase. If we add search, 7x performance increse with constant model size.
- Scaling test time compute makes experiments more expensive in certain cases.

### AI in Go

- No pure-NN model with no planning has beaten humans before.
- Pure planning agent (AlphaGo Zero) with zero human domain knowledge beats all models with prior human knowledge.
- In general: increasing 120 points requires either:
  - 2x model size and training
  - 2x test-time search
  Therefore, to scale pure-NN model with no search to the level of AlphaGo Zero requires ~100,000x increase in model-size and data.

### Scaling Laws in RL

- 15x test time compute is equivalent to 10x training compute.

### Planning in Chess

- With planning, prediction accuracy of human moves is better than without planning.

### Takeaways

- Planning works _SUPER_ well for NP hard problems, ie: math, programming, puzzles.
- Human reasoning is in general an NP hard problem.
