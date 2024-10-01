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

### Planning in LLMs

- Minerva is a math dataset test, most models struggle to get > 40% correct.
- Deepmind used consensus to get over 50% performance.
  - Consensus is where you generate many samples and select the best one (how?)
- Best-of-N beats consensus, but you need a reward model.
  - Best-of-N generates many samples and rates the output with a reward model.
- Let's Verify Step-by-Step
  - Perform MCTS with language, then use the output result to apply rewards to the branches that lead to correct results.


### Self Thoughts

- Planning works _SUPER_ well for NP hard problems, ie: math, programming, puzzles.
- Human reasoning is in general an NP hard problem.
- Have we reached the limit of scaling? Planning seems to be the next thing forward.
